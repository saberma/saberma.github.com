---                                                                                                                                                          
layout: post
title: 在Ubuntu环境下安装最土团购开源php程序
category: php
#published: false
---

h2. 下载

在最土官网下载
"http://notice.zuitu.com/down/ZuituGo_V1.6.tar.gz":http://notice.zuitu.com/down/ZuituGo_V1.6.tar.gz

h2. 安装mysql数据库

sudo apt-get install mysql-server

h2. 安装php环境

pre. 
#apache
sudo apt-get install apache2
#php5(默认带有mbstring组件)
sudo apt-get install php5
#配置APACHE+PHP
sudo apt-get install libapache2-mod-php5
sudo /etc/init.d/apache2 restart
#mysql相关
sudo apt-get install libapache2-mod-auth-mysql
sudo apt-get install php5-mysql
sudo /etc/init.d/apache2 restart

h2. 安装程序

将压缩包中的wwwroot下的内容解压至/var/www目录下

bq. 
如需修改apache webroot黙认路径，请修改/etc/apache2/sites-enabled/000-default文件

pre. 
#修改目录归属于当前用户saberma
chown saberma /var/www
#修改目录可写权限
chmod a+w static/user
chmod a+w static/team
chmod a+w include/compiled
chmod a+w include/configure
chmod a+w include/data/
chmod a+w include/template/

h2. 配置

浏览器打开 @http://localhost/install.php@
输入数据库密码后点击安装即可

安装完成后需要手动删除install.php

pre. 
rm /var/www/install.php

h2. 调试

h3. 打开日志

为方便初期调试错误，需要打开错误日志输出功能
修改 @/etc/php5/apache2/php.ini@ 的以下内容

bq. 
error_reporting = E_ALL
display_error = on
html_errors = on
log_errors = off

h3. 查看php环境信息

在网站根目录中新增phpinfo.php文件，内容如下

bq. 
<?php phpinfo()?>

浏览器打开phpinfo.php，即可看到环境信息

h2. 部署

一般情况下，PHP的虚拟空间只能使用ftp，而项目代码使用git进行版本控制
为了让git管理下的项目代码能够直接发送至ftp，需要安装 "git-ftp":http://github.com/resmo/git-ftp

pre. 
#ubuntu10.04下的安装过程
sudo -s
add-apt-repository ppa:resmo/git-ftp
aptitude update
aptitude install git-ftp
exit

配置ftp地址、用户名及密码

pre. 
git config git-ftp.user saberma
git config git-ftp.url ftp.example.com
git config git-ftp.password secr3t

修改文件后，使用以下命令部署

bq. 
git ftp push

h2. 参考资源

"最土官网下载说明":http://www.zuitu.com/download_new.html
