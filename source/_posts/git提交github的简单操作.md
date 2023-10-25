---
title: Git提交github的简单操作
date: 2023-10-11 16:27:20
tags: 
	- git
	- github
categories: 日常软件知识
comments: true
---

# github查找资源技巧
找百科：awosame xxx
例子：xxx sample
教程：xxx tutorial
项目配置空架子：xxx starter/xxx boilerplate

## git的使用
本地的git仓库文件夹都会有一个.git隐藏文件作为仓库的初始化文件
git init =>手动初始化文件夹
插件  gitlens

<!--more-->
### 项目的提交基本操作
```
git clone xxx 托库
git add -A 提交本地仓库所有文件
git commit -m '提交信息记录'
git log --stat   查看历史提交记录
```
工作区（本地仓库）--git add <file> ---->暂存区----git commit----->网上仓库（github）


### 项目的维护操作
```
git checkout filename  回滚到上次修改，但前提是没有commit
git reset HEAD^1 commit后将最新提交返回到暂存区
```

### 分支
```
以当前分支为基础创建分支
git checkout -b  <branchname>
列举所有分支
git branch
切换到某个分支
git checkout <branchname>
删除特定分支
git checkout -D <branchname>
合并分支
git merge <branchname>
放弃合并
git merge --abort
```

### git与github远程仓库
```
推送
git push
拉取
git pull
```



### push大文件到github
```
git lfs track 查看LFS管理的所有类型文件
git add .  #将工程文件全部添加进commit提交列表中
git commit -m 'Add new large assets' #附加提交日志
git lfs ls-files #查看LFS管理了哪些文件
git push #全仓库推送
cd upload #进入名为upload的文件夹，提前将要上传的大文件放入该文件夹下
git init #创建本地仓库环境
git lfs install #安装大文件上传应用
git lfs track */"*.exe" #追踪要上传的大文件，*表示路径下的所有文件
git add .gitattributes #添加先上传的属性文件(要先上传属性文件，不然有可能失败)
git commit -m "pre" #添加属性文件上传的说明
git remote add origin https://github.com/xxxx.git #建立本地和Github仓库的链接，.git链接在网页端clone的HTTPS中获取
git push origin master #上传属性文件
git add -A #添加要上传的大文件，*表示路径下的所有文件
git commit -m "Git LFS commit" #添加大文件上传的说明
git push origin master #上传大文件
git pull --rebase origin master
git push -u origin master
```





```
git代理设置
 git config --global http.proxy http://127.0.0.1:7890
 git config --global https.proxy https://127.0.0.1:7890
 
git取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

git缓冲区设置
git config http.postBuffer 1024000
```