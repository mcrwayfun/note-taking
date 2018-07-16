# git
1. 初次使用git配置以及git如何使用ssh密钥（将ssh密钥添加到github）：[https://www.cnblogs.com/superGG1990/p/6844952.html](https://www.cnblogs.com/superGG1990/p/6844952.html)

2. git add 和 git commit 命令合一
```
git commit -m msg file1 file2
```

3. git add、git commit and git push 合一
在git安装目录下的/etc/.bashrc文件中添加一个function
```
function lazygit() {
    git add .
    git commit -a -m "$1"
    git push
}
```

4. 如果commit注释写错了，只是想改一下注释
```
git commit --amend
```
此时会进入默认vim编辑器，修改注释完毕后保存就好了。