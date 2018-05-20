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