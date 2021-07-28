### 常用操作
#### 常用命令
```bash
# clone代码
git clone git@github.com:xxx/xxx.git
# 查看并并从远程分支创建新分支
git branch -a
git checkout -b private-cloud-new-branch origin/master
#推送代码
git add -A
git commit -m "arvon test ssy-08 ok"
git push origin private-cloud-new-branch
#放弃本地修改
git checkout .
git clean -df
#获取远端最新代码
##第一种方法
git fetch --all
git reset --hard origin/master
git fetch下载远程最新的， 然后，git reset master分支重置
##第二种方法
git reset --hard HEAD
git pull

#暂存提交
git add demo.html // 提交到暂存区
git stash -u -k  // 忽略其他修改，关键一步
git commit -m '修改演示文件' // 提交暂存区
git pull // 拉取合并
git push origin master // 推到远程仓库
git stash pop // 恢复之前忽略的文件（非常重要的一步）
```
#### 修改config设置区分大小写
```bash
Add ignorecase = false to [core] in .git/config;
```

```bash
git config user.name "Gourds"
git config user.email gourds@yeah.net
```
