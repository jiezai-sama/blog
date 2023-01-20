---
title: hexo博客的搭建并部署云服务器
date: 2022-11-22 19:40:05
tags: 
- nginx
- hexo
- butterfly
categories: 
- 博客
cover: /image/cover4.jpg
sticky: 10000
---
## 1 hexo博客的搭建
### 1.1 git安装
-- 首先需要下载，官网地址：https://git-scm.com/
-- 再进行安装，具体图文步骤见 https://blog.csdn.net/weixin_47638941/article/details/120632890
### 1.2 node.js安装
官方地址：http://nodejs.cn/download/
具体步骤：https://blog.csdn.net/qq_48485223/article/details/122709354
### 1.3 hexo的搭建
全局安装hexo-cli
```SHELL
npm install -g hexo-cli
```
创建目录并安装依赖
```SHELL
hexo init <folder>   # folder为你的博客目录名字，任取
cd <folder>          # 进入该目录
npm install          # 安装依赖
```
## 2 hexo博客部署到云服务器
### 2.1 准备工作
-- 本地搭建完成的博客
-- 一台本地电脑
-- 一台云服务器(此次使用的华为云)
-- ssh连接工具(xshell、华为云自带的cloudshell等)
### 2.2 连接云端
通过xShell或其他ssh连接软件连接  
输入买的华为云的用户密码建立连接
### 2.3 git创建
安装依赖包
```SHELL
yum install -y curl-devel expat-devel gettext-devel openssl-devel zlib-devel 
```
安装编译工具
```SHELL
yum install -y gcc perl-ExtUtils-MakeMaker package
```
安装git
```SHELL
yum install git      # 安装
git -vesion          # 查看git版本
```
### 2.4 创建用户并配置免密登录
#### 2.4.1 用户创建
创建用户并设置密码
这里的username改成你自己的用户名，并且为用户设置一个密码。
```SHELL
adduser [username]
passwd [username]
```
为用户分配权限
将用户添加到wheel组
```SHELL
usermod [username] -G wheel
```
#### 2.4.2 配置免密登录
本地创建密钥
在本机电脑终端输入
```SHELL
ssh-keygen -t rsa 
```
一直回车，新建一个密钥。
命令行会提示密钥文件的路径，window电脑路径一般是<font color=#FF0000 >C:\Users\Administrator\.ssh</font>，复制<font color=#FF0000 >id_rsa.pub</font>文件中的内容
#### 2.4.3 配置公钥
回到服务器端，切换用户
```SHELL
su - [username]
```
创建.ssh文件夹
```SHELL
mkdir .ssh
```
在.ssh文件夹中新建authorized_keys
```SHELL
vim .ssh/authorized_keys
```
将刚刚id_rsa.pub公钥中的内容，复制粘贴到文件里，保存退出,:wq为保存退出
在本地Git终端中测试是否能免密登录git
```SHELL
ssh -v git@server # server为云主机ip
```
#### 2.4.4建立git仓库并配置
回到root用户，创建git目录，并修改目录所有权和用户权限
```SHELL
exit                                         # 回到root
mkdir /home/git/
chown -R [username]:[username] /home/git/
chmod -R 755 /home/git/
```
进入目录，建立git仓库，修改权限
```SHELL
cd /home/git/
git init --bare blog.git
chown [username]:[username] -R blog.git
```
在hooks文件夹下新建钩子文件 post-receive,并粘贴下列代码
```SHELL
vim /home/git/blog.git/hooks/post-receive
```
```TEXT
以下为粘贴内容
#!/bin/bash
git --work-tree=/home/blog --git-dir=/home/git/blog.git checkout -f
```
保存退出，并修改文件权限
```SHELL
chmod +x /home/git/blog.git/hooks/post-receive
```
### 2.5 nginx配置
#### 2.5.1首先创建blog目录并赋予权限
```SHELL
mkdir /home/blog/
chown -R [username]:[username] /home/blog/
chmod -R 755 /home/blog/
```
#### 2.5.2 安装nginx
安装
```SHELL
yum install -y nginx
```
启动nginx
```SHELL
systemctl start nginx.service
```
查看nginx服务状态，绿色的active说明启动成功
```SHELL
systemctl status nginx.service
```
在浏览器中输入服务器公网ip地址，测试是否能打开
#### 2.5.3 修改nginx转发配置
查看nginx的默认配置文件的安装位置
```SHELL
nginx -t
```
修改nginx的默认配置，其中cd后边就是刚刚查到的安装位置，每个人可能都不一样
我这里是<font color=#FF0000 >/etc/nginx/nginx.conf</font>
```SHELL
vim /etc/nginx/nginx.conf
```
在其中找到如下位置并作出修改
```SHELL
user root     # 将用户改为root
```
```SHELL
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /home/blog;                 # 修改为创建的blog目录地址
    server_name www.example.com;     # www.example.com =>需要修改为你的域名（或者公网ip）
    
    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;
    location / {
    }
    error_page 404 /404.html;
        location = /40x.html {
    }

```
保存退出并重启nginx
```SHELL
systemctl restart nginx.service
```
### 2.6 在hexo站点配置文件中配置
打开本地的hexo博客根目录，打开配置文件_config.yml,修改以下内容
```YML
deploy:
  type: git
  repo: [username]@100.0.0.100:/home/git/blog.git   #用户名@服务器Ip:git仓库位置
  branch: master
```
在根目录下打开终端，部署
```SHELL
hexo clean
hexo g
hexo d
```
如果提示ERROR Deployer not found:git错误，先安装：
```SHELL
npm install hexo-deployer-git --save
```
### 2.7 nginx常用命令
```SHELL
# 停止开机自启动
systemctl disable nginx.service
#设置nginx服务开机自启动
systemctl enable nginx.service
#启动nginx服务
systemctl start nginx.service
#停止nginx服务
systemctl stop nginx.service
#重启nginx服务
systemctl restart nginx.service
#重新读取nginx配置(这个最常用, 不用停止nginx服务就能使修改的配置生效)
systemctl reload nginx.service
# 查看服务当前状态
systemctl status nginx.service
# 查看所有已启动的服务
systemctl list-units --type=service
```




