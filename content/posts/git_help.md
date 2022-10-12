---
title: "Git 简单入门"
date: 2022-08-22T21:14:53+08:00
draft: false
tags: ["git"]
---

本文旨在帮助学习和回忆git相关命令
<!--more-->

## 基本技术

![image-20220820004659891](https://img-blog.csdnimg.cn/fe7554cf09cd482dada374feec7ac3b1.png)

`git config --global user.name （user name)`

`git config --global user.email (email)`

设置本地仓库用户名和邮箱

`git init`初始化本地仓库

`git status`查看：当前所处分支，准备commit的changes，untracked files

`git add`添加untracked file进缓存区

`git commit`提交，会进入编辑器提示添加commit message，可以通过`git config core.editor vim`将编辑器修改为vim

`git log`查看前面的版本

`source .gitignore`新建文件，里面放不想推送的仓库（不想被git追踪）的文件名，坏处是一旦删除本地文件就没办法找回

`git branch (branch name)`创建分支

`git branch`查看所有分支

`git checkout (branch name)`切换分支

`git commit -a -m (commit message)`或`git commit -am (commit message)`同时完成add和commit操作

`git branch -d (branch name)`删除分支，删除还未合并(merge)的分支会删除失败

`git branch -D (branch name)`强制删除还未合并(merge)的分支

`git checkout -b (branch name)`创建新分支并马上转到新分支

`git merge (branch name)`把别的分支(branch name)合并到当前的分支

当merge时可能出现修改冲突，就需要手动处理（文件上会显示）

`git clone (url)`拷贝远程仓库到本地

`git remote -v`查看本地仓库和哪些远程仓库有联系

`git push`在push时需要输入用户名和密码，用户名就是随便起的别名，标识身份用，密码时github上developer setting中的token(注意一旦设置一个新的token记得保存，不然就找不到了，就得删了token再设置新的【悲】)

`git fetch []`将远程仓库拉到本地，可以指定某个仓库(上一条命令查看已链接的仓库)（如果链接的仓库多的话）也可以不指定

`git diff (远程仓库名)/(分支名)`查看区别

fetch完之后会形成一个类似独立的分支一样的版本库，用`git merge [origin]/[master]`将版本库拉进某个分支，origin可以是其他名字（`git remote -v`查看名字），master可以是其他分支名

`git log`也可以查看这种方式修改本地的版本历史

## 其他

+ remote相关
  + `git remote add [shortname] [url]`添加远程版本库
  + `git remote rm name`删除远程仓库
  + `git remote rename old_name new_name`修改仓库名
+ push相关
  + `git push (远程主机名) (本地分支名):(远程分支名)`（这个顺序也很有意思，从本地推远）
  + 如果本地分支名和远程分支名相同则可以省略冒号：`git push (远程主机名) (本地分支名)`
+ pull相关
  + pull就是fetch和merge的结合
  + `git pull (远程主机名) (远程分支名):(本地分支名)`（从远程拉到本地）
  + 如果本地分支名和远程分支名相同则可以省略冒号：`git pull (远程主机名) (远程分支名)`

血泪教训，最好先git clone一下再push、pull之类的，不然会有奇奇怪怪的问题
