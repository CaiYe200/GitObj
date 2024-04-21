##### 配置名字和邮箱

![image-20240420205741023](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240420205741023.png)

0311cyz@LAPTOP-B3NHP9EM MINGW64 ~/Desktop
$ git config --global user.name "caiyezhao"

0311cyz@LAPTOP-B3NHP9EM MINGW64 ~/Desktop
$ git config --global user.email "1354827080@qq.com"

0311cyz@LAPTOP-B3NHP9EM MINGW64 ~/Desktop
$ git config --global user.name
caiyezhao

##### 配置别名

1,在用户文件夹下，创建.bashrc,

2,往文件中创建别名: alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'

3,在git bash中，source ~/.bashrc

##### git基本操作

git创建或修改为存放在工作区，add之后放在暂存区，commit放在仓库

git add fielname/. : filename即文件名字，.为全部

git commit -m ‘text’ ：提交，-m ‘text’为标注该操作做了什么事

git log :查看日志

git reset --hard x：x为操作的唯一标识号，回滚

git reflog：查看所有事务操作

##### 创建分支

git branch：查看分支

git branch dev01：创建分支dev01

git checkout dev01：进入分支dev01

git checkout -b dev02 ：进入dev02，如果没有就创建

git branch -d dev02 ：删除dev02分支

git merge dev01：把分支dev01合并到master中

解决冲突：若两个分支同时修改了一个文件的同一数据，并合并在一起，则会在文件中返回两个修改的结果，让使用者自己选择，选择完应add和commit