## windows
1. 如何生成一个项目结构树，比如现有项目spring-data-jpa-h2
```
tree spring-boot-jpa-h2 > list.txt
```
在list.txt中生成树形结构

## 配置临时域名

打开**C:\Windows\System32\drivers\etc**，找到hosts文件，用管理员的方式编辑hosts文件，可以配置临时域名

```
127.0.0.1 mcrwayfun.com
```
如上，我可以通过`http://mcrwayfun.com:8080/login`来替代`http://127.0.0.1:8080/login`进行访问