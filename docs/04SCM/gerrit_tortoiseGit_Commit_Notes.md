



#### ----为每次推送生成change-ID

copy commit-msg 文件到C:\Users\Administrator\.git_template\hooks目录下

chmod u+x ~/.git_template/hooks/commit-msg

git config --global init.templatedir ~/.git_template
————————————————

进入“Network”配置页面，修改‘ssh client’为git 的ssh.exe,如下：

![img](https://images2018.cnblogs.com/blog/1282849/201806/1282849-20180605160819077-850813431.png)





（4）针对gerrit code review，我们在执行push的时候，希望的是想gerrit提交，等待reviewer审核，但是直接使用小乌龟git来push代码时，并没有这样的效果，此时需要需要再提交的时候，在“Remote”显示的分支前加上“refs/for/”字段，如下：

![img](https://images2018.cnblogs.com/blog/1282849/201806/1282849-20180605161415838-1222511597.png)

![img](https://images2018.cnblogs.com/blog/1282849/201806/1282849-20180605161608894-1173688104.png)

点击ok即可，如下：

![img](https://images2018.cnblogs.com/blog/1282849/201806/1282849-20180605161749829-770038801.png)

此时去gerrit上看到刚才的commit为“test2”的提交，如下：

![img](https://images2018.cnblogs.com/blog/1282849/201806/1282849-20180605161859500-401922484.png)

证明提交到gerrit上成功，只要review ok即可合入仓库。

**另外更改.git/config****文件,添加“push”部分的配置，是不行的**，（具体原因不清楚，大家清楚的可以评论里说明下，分享给大家学习下~_~）如下：

![img](https://images2018.cnblogs.com/blog/1282849/201806/1282849-20180605162527080-1524574141.png)







#### It seems to me that the checkbox "Always push to the selected remote branch for this local branch" should do this.