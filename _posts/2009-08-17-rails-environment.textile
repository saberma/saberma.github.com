---
layout: post
title: Rails环境安装
category: rails
---
h2. 操作系统安装

<pre>
#安装ubuntu8.04桌面版(服务器装服务器版)
#之后修改源配置
sudo vi /etc/apt/sources.list

#注意，以下是8.04版本的
#按cG键，删除所有内容，按ctrl+shift+v粘贴以下内容
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

#粘贴后按Esc键，再输入:wq，注意是打冒号wq，保存并退出vi
#更新源
sudo apt-get update

#注意如果是9.04的版本，源列表内容如下：
deb http://mirrors.163.com/ubuntu/ jaunty main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ jaunty-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ jaunty-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ jaunty-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ jaunty-backports main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ jaunty main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ jaunty-security main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ jaunty-updates main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ jaunty-proposed main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ jaunty-backports main restricted universe multiverse 
</pre>

h2. 开发环境安装

<pre>
#服务器、开发机都需要以下依赖库
sudo apt-get install ruby libopenssl-ruby1.8 mysql-server mysql-client libmysqlclient15-dev git-core imagemagick nginx ruby1.8-dev
#手动安装 rubygems
wget http://rubyforge.org/frs/download.php/57643/rubygems-1.3.4.tgz
tar -xvf rubygems-1.3.4.tgz
cd rubygems-1.3.4
sudo ruby setup.rb
#添加gem源
gem sources -a http://gems.github.com
gem sources -a http://rubygems.org
  #敲上面命令如果说找不到gem命令，先执行
  sudo ln -s /usr/bin/gem1.8 /usr/bin/gem
#安装webrat的依赖库
sudo apt-get install libxslt1-dev libxml2-dev
#安装rails及其他库
sudo gem install rails --version='2.3.2' --no-ri --no-rdoc
sudo gem install rspec hoe haml thoughtbot-paperclip mislav-will_paginate mysql mongrel mongrel_cluster aslakhellesoy-cucumber webrat javan-whenever ferret acts_as_ferret rmmseg --no-ri --no-rdoc
sudo gem install handsoap httpclient searchlogic awesome-print -no-ri --no-rdoc
#开发机额外安装(xosd-bin及以后的内容都是autotest所需)
sudo apt-get install irb ctags xosd-bin ruby-gnome2 libnotify-dev
sudo gem install rspec-rails ZenTest diff-lcs thoughtbot-factory_girl --no-ri --no-rdoc

#安装日历插件(已安装至项目的plugin目录下)
#sudo gem install calendar_date_select --no-ri --no-rdoc
</pre>

"安装Oracle适配器":/2009/08/15/rails-oracle.html
"安装SqlServer适配器":/2009/08/15/rails-sqlserver.html
"安装memcache及cache-money":/2009/11/03/memcached.html

为支持测试结果的提示，需要手动安装ruby-libnotify

<pre>
#需先安装ruby-gnome2 libnotify-dev
wget http://rubyforge.org/frs/download.php/27134/ruby-libnotify-0.3.3.tar.bz2
#进入ruby-libnotify-0.3.3.tar.bz2所在目录
tar -vxf ruby-libnotify-0.3.3.tar.bz2
#参考INSTALL文件执行:
cd ruby-libnotify-0.3.3
ruby extconf.rb
sudo make && sudo make install
#加入autotest运行结果在右上角任务栏的提示效果图标
git clone git://github.com/saberma/autotest_images.git  ~/.autotest_images
</pre>

"安装浏览器开发插件Web Development":https://addons.mozilla.org/en-US/firefox/downloads/latest/60
"安装Vim":/2009/10/10/vim.html
"安装全文检索Ferret":/2009/07/12/rails-search-engine-ferret.html
"安装短信机客户端环境":/2009/08/15/rails-sqlserver.html

h2. 获取svn项目源代码

具体内容参考以下内容
"svn集中存储":/2009/09/09/git-to-svn.html

h2. -获取github源代码-

此节内容不再使用，改为使用svn集中存储

<pre>
#注意，如果系统转换成中文的话，Documents名称会被改成[文档]，建议转成中文的时候不要修改目录名
cd ~/Documents
#请使用svn集中存储的说明获取源代码
-git clone git@github.com:cogentsoft/zbs.git  -
cd zbs
#模块化插件不好用，不再使用
-#初始化子模块-
-git submodule init-
-git submodule update-
#修改数据库配置：用户名、密码(注意yml中用户名、密码的值前面要有空格)
vim config/database.yml
#创建数据库
rake db:create:all
rake db:migrate
#使用以下语句启动应用服务器
mongrel_rails start
打开浏览器，输入地址http://localhost:3000，是否看到首页了？
#如果需要进行应用前缀，则这样启动
mongrel_rails start --prefix=/zbs
打开浏览器，输入地址http://localhost:3000/zbs
#或者启动app应用服务器集群，试运行(以下方式启动后，打开的浏览器地址应为http://localhost:4000)
mkdir tmp
mkdir tmp/pids
mongrel_rails cluster::start
</pre>

h2. 搭建本地测试环境

h3. 开启本地开发过程的测试监控

<pre>
script/autospec
</pre>

h3. 运行验收测试

<pre>
mongrel_rails cluster::start -d -e test
# rake是rails最常用的命令，可以通过rake --tasks db查看帮助文档，db为要查询的字符串
rake test:acceptance
</pre>

h2. mysql常用命令

<pre>
# 打开客户端连接Mysql
script/dbconsole -p
  #查看当前状态
  status
  #查看数据库
  show databases;
  #选择数据库
  use zbs_development;
  #查看表
  show tables;
  #查看表结构
  desc user;
</pre>

bq. TODO:将以上常用命令独立成篇
