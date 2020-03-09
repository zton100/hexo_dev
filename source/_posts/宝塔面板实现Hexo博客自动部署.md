---
title: 宝塔面板实现Hexo博客自动部署
categories: 笔记
tags:
  - hexo
  - 宝塔面板
cover: 'https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1051132835,1344326547&fm=26&gp=0.jpg'
abbrlink: 16708f1b
date: 2020-03-08 00:52:10
top_img: /img/thumb-1920-712781.jpg
---
## 安装webhook
进入宝塔面板，依次进入：软件商店->宝塔插件，在列表里可以看到宝塔WebHook插件，点击后面的安装即可。
## 在服务器上生成部署公钥
``` bash
cd ~/.ssh/
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
ssh-keygen -t rsa -C "你的邮箱"
```
依次执行以上命令, 生成部署公钥的时候直接回车后面直接回车，不要输入密码，这样使用公钥的时候就无需密码了.
然后执行`cat ~/.ssh/id_rsa.pub`获取公钥

## 在github上对项目作部署配置添加公钥
登陆github, 然后`Settings -> SSH and GPG keys -> New SSH key`, 标题随意写,将公钥复制到key里面, 最后点击`Add SSH key`保存
![示例图](https://s2.ax1x.com/2020/03/08/3vuXWV.png)

## 添加webhook
```bash
#!/bin/bash
echo ""
# 输出当前时间
date --date='0 days ago' "+%Y-%m-%d %H:%M:%S"
echo "Start"
# 判断宝塔WebHook参数是否存在
if [ ! -n "$1" ];
then
          echo "param参数错误"
          echo "End"
          exit
fi
# git项目路径
gitPath="/www/wwwroot/$1"
# git 网址
gitHttp="http://git.xxxxx.com/$1.git"

echo "Web站点路径：$gitPath"

# 判断项目路径是否存在
if [ -d "$gitPath" ]; then
        cd $gitPath
        # 判断是否存在git目录
        if [ ! -d ".git" ]; then
                echo "在该目录下克隆 git"
                git clone $gitHttp gittemp
                mv gittemp/.git .
                rm -rf gittemp
        fi
        # 拉取最新的项目文件
        git reset --hard origin/master
        git pull
        # 设置目录权限
        chown -R www:www $gitPath
        echo "End"
        exit
else
        echo "该项目路径不存在"
        echo "End"
        exit
fi
```
添加完成以后，查看密钥,组成的链接是这样的：`http://面板Ip加端口/hook?access_key=密钥&param=项目在/www/wwwroot/目录下的目录`
## 配置github webhooks
把github webhooks钩子把组成链接填在payload URL里, Secret可不填, 大功告成!
![示例图](https://s2.ax1x.com/2020/03/08/3vMCjS.png) 

>当执行 `hexo g -d` 将hexo推到git的时候, 会通过webhooks钩子将更新的文件自动部署到宝塔.

