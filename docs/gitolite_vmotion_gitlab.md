### 方案1：

第一步,将gitolite复制到gitlab仓库目录下

```
# cp -r /gitroot/gitolite/repositories/* /var/opt/gitlab/git-data/repositories/
```

执行导入处理程序

```
# gitlab-rake gitlab:import:repos
```

上面程序会处理一下目录结构，例如

进入gitlab web界面，创建仓库与导入的仓库同名，这样就完成了导入工作。

|                                                              | 提示 |
| :----------------------------------------------------------- | ---- |
| 转换最好在git用户下面操作，否则你需要运行`# chown git:git -R /var/opt/gitlab/git-data/repositories` |      |



### 方案二

​		第一步

-  ssh -p38168 git@git.smartfenda.cn | awk '/^[ @]*R/{print $NF}'

  第二步

  ![img](http://static.oschina.net/uploads/space/2016/0304/134557_DV4M_2496664.png)

  根据上面的仓库列表，使用API或者command-line来创建GitLab仓库

  初始化远程仓库的脚本git-init-remote

```shell

#!/bin/sh
 
repo=$1
token=put_your_api_token_here
 
test -z $repo && echo "Repo name required." 1>&2 && exit 1
 
curl -H "Content-Type:application/json" https://gitlab.com/api/v3/projects?private_token=$token -d "{ \"name\": \"$repo\" }"
```



  	第三步

​		下载Gitolite项目到本地仓库

```shell
#!/bin/sh
if [ “$#” -ne 5 ]; 
then echo “Usage: $0 GITOLITE GITLAB_URL API_TOKEN GITLAB_USER REPONAME” >&2 
echo “For example: $0 git@your.gitolite.com your.gitlab.com yoursecrettoken a-cool-repo” 
exit 1 
fi
GITOLITE=$1 GITLAB_URL=$2 API_TOKEN=$3 GITLAB_USER=$4 REPO_NAME=$5
git clone –mirror $GITOLITE:$REPO_NAME.git 
 
./gitlab-init-remote $GITLAB_URL $API_TOKEN $REPO_NAME cd $REPO_NAME.git
```



第四步

同步本地仓库到GitLab服务器上

```shell
git remote add gitlab git@$GITLAB_URL:$GITLAB_USER/$REPO_NAME.git
 git push -f –tags gitlab refs/heads/:refs/heads/
```

