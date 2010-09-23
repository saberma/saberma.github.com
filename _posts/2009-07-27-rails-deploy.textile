---                                                                                                                                                          
layout: post
title: 服务器部署
category: rails
---
h1. 服务器安装配置

<pre>
#注意，安装时一定要选上安装openssh
#安装完成后，在开发机ssh远程登录
ssh 202.202.202.202 -l admin_user_name
#修改源
sudo vi /etc/apt/source.slist
#清空内容，加入以下内容
deb http://mirrors.163.com/ubuntu/ hardy main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ hardy-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ hardy-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ hardy-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ hardy-backports main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ hardy main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ hardy-security main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ hardy-updates main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ hardy-proposed main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ hardy-backports main restricted universe multiverse
#保存后运行
sudo apt-get update
</pre>

h1. 环境安装

<pre>
#在开发机上安装
sudo gem install deprec
#指定服务器IP地址
export HOSTS=202.202.202.202
#修改服务器root密码，新增一个部署专用的用户(自己安装过程中已经新增用户时，以下两步可省)
cap deprec:users:passwd USER=root
cap deprec:users:add USER=root
#配置ssh，以后登录服务器无需密码
cap deprec:ssh:setup_keys
cap deprec:ssh:config_gen
cap deprec:ssh:config
#为服务器安装nginx,mongrel,mysql,ruby,rails
cap deprec:rails:install_stack
#安装mysql数据库
cap deprec:db:install
</pre>

<pre>
#修改数据库编码
sudo vi /etc/mysql/my.cnf
#在[client]中增加参数
default-character-set=utf8
#在[mysqld]中增加参数
default-character-set=utf8
init_connect    = 'SET NAMES utf8'
#重启mysql
/etc/init.d/mysql restart
</pre>

<pre>
#先ssh登录服务器，加入github gem source
ssh 202.202.202.202
gem sources -a http://gems.github.com
</pre>

<pre>
#开发机执行
#下载程序，安装gem插件
cap deploy:setup
#部署程序至服务器
cap deploy
#执行数据库脚本
cap deploy:migrate
</pre>

h1. 注意事项

经测试，deprec当前支持的ubuntu最新版本为8.04
生产环境下要加一个事件，检查全文检索是否生效(无事件记录时可能不会产生索引文件的问题)

h1. 故障排除

<pre>
#运行cap deprec:users:passwd USER=root，出现以下错误
(Net::SSH::AuthenticationFailed: cogentzbs)
#先将deploy.rb中的set user "cogentzbs"改为服务器超級用户名称，成功运行命令后再改回来
</pre>

h1. 配置

h1. 启动

<pre>
#启动全文检索ferret服务
script/ferret_server start --root=. -e production
</pre>

h1. 参考资料

"capistrano官网":http://www.capify.org
"deprec指南":http://deprec.failmode.com/documentation/example-tracks/
"oracle连接":http://wiki.rubyonrails.org/database-support/oracle
