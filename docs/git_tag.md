git 拉取tag, 识别最新tag,在此版本上添加tag



通过shell脚本自动获取最新tag,并输入最新版本，推到git上

```shell
#/bin/bash

#拉取分支上现有tags
git fetch --tags

echo -e "所有tag列表"
git tab -l -n

echo -e "${taglist}"

#获取最新版本tag

LastestTag=$(git describe --tags `git rev-list --tags --max-count=1`)

echo -e " 最新版本tag......"
echo -e "$LastestTag"

echo -e "请输入要新增的版本号...... 如v1.0.1"
#输入tag名称

read  tagName

git tag ${tagName}

#推到分支上

git push origin ${tagName}
```





