# git常用命令

## git-bash报路径不存在，中文文件乱码
更改配置：git config –-global core.quotepath false

## 初次使用git配置以及git如何使用ssh密钥（将ssh密钥添加到github）
[https://www.cnblogs.com/superGG1990/p/6844952.html](https://www.cnblogs.com/superGG1990/p/6844952.html)

## git add 和 git commit 命令合一
```
git commit -m msg file1 file2
```

## git add、git commit and git push 合一
在git安装目录下的/etc/.bashrc文件中添加一个function
```
function lazygit() {
    git add .
    git commit -a -m "$1"
    git push
}
```

## 如果commit注释写错了，只是想改一下注释
```
git commit --amend
```
此时会进入默认vim编辑器，修改注释完毕后保存就好了。

## 修改commit信息
1、 将当前分支无关的工作状态进行暂存
```
git stash
```
2、 将 HEAD 移动到需要修改的 commit 上
```
git rebase 9633cf0919^ --interactive
```
3、找到需要修改的 commit ,将首行的 pick 改成 edit
4、开始着手解决你的 bug
5、git add 将改动文件添加到暂存
6、git commit –amend 追加改动到提交
7、git rebase –continue 移动 HEAD 回最新的 commit
8、恢复之前的工作状态，git stash pop

## 强制更新本地文件
```
git fetch - -all 
git reset - -hard origin/master 
```