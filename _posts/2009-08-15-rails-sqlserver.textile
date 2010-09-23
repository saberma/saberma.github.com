---                                                                                                                                                          
layout: post
title: SQLServer on Rails
category: rails
---
h1. 安装

h2. 安装库

h3. 安装unixODBC

<pre>
sudo apt-get install unixodbc unixodbc-dev
</pre>

h3. 安装sqlserver驱动freetds

<pre>
cd ~
wget  http://cloud.github.com/downloads/saberma/saberma.github.com/freetds-stable.tgz
tar -vxf freetds-stable.tgz
cd freetds-0.82
./configure --prefix=/usr/local/freetds --sysconfdir=/etc --with-tdsver=8.0
sudo make
sudo make install
</pre>

h3. 附配置文件内容

<pre>
#将config/etc下的文件拷贝至/etc
cd ~/Documents/zbs
sudo cp config/etc/* /etc
</pre>

配置文件中的内容不需要修改，实际生产环境中才修改/etc/freetds.conf的SQL Server服务器地址
注意：不要在配置文件中有多余的空格，否则就算配置正确，unixODBC也会报以下奇怪的错误信息

<pre>
[IM002][unixODBC][Driver Manager]Data source name not found, and no default driver specified
[ISQL]ERROR: Could not SQLConnect
</pre>

以下为config/etc下的文件内容

/etc/odbc.ini

<pre>
[SQL2000DSN]
Driver=FreeTDS
Description=ODBC Connection via FreeTDS                                                                                                                      
Trace=No
Servername=SQL2000HOST
</pre>

/etc/odbcinst.ini

<pre>
[FreeTDS]
Description=TDS driver (Sybase/MS SQL)
Driver=/usr/local/freetds/lib/libtdsodbc.so
CPTimeout=
CPReuse=
FileUsage=1
</pre>

/etc/freetds.conf

<pre>
[SQL2000HOST]
  host = 192.168.1.13
  port = 1433
  tds version = 8.0 
  client charset = UTF-8
</pre>

h3. 测试

<pre>
#测试是否连接成功,tsql命令在freetds子目录bin下
cd /usr/local/freetds
TDSVER=8.0 bin/tsql -H 192.168.1.13 -U msg -p 1433
#提示你输入密码时，输入msg
#提示1 >，表示正常连接至数据库了,输入exit关闭连接
exit

#测试使用配置文件的方式连接
bin/tsql -S SQL2000HOST -U msg -p 1433
#查看freetds.conf所在位置
bin/tsql -C
</pre>

<pre>
isql -v SQL2000DSN msg msg
#unixODBC调试命令
#查看unixODBC配置文件所在位置
odbcinst -j
#查看unixODBC已有DSN
odbcinst -q -s
</pre>

h2. 安装ruby-odbc

<pre>
cd ~
wget http://cloud.github.com/downloads/saberma/saberma.github.com/ruby-odbc-0.9996.tar.gz
tar -xvzpf ruby-odbc*gz
cd ruby-odbc*[0-9]*
ruby extconf.rb
sudo make
#如果上面命令出错,报nothing to be done for all , 则执行: make clean
sudo make install
</pre>

h2. 安装gems

<pre>
sudo gem install dbi --version 0.4.0 --no-ri --no-rdoc
sudo gem install dbd-odbc --version 0.2.4 --no-ri --no-rdoc
sudo gem install rails-sqlserver-2000-2005-adapter --no-ri --no-rdoc
</pre>

h2. 测试

<pre>
script/console
#获取tbl_smsendtask表信息,正常的话会返回Msg所有的字段
Msg
#看一下中文显示是否正常
Msg.last
#修改为中文
m = Msg.last
m.update_attribute :SM_Content, '中文字符'
#查看修改是否生效
Msg.all.collect {|m| m.SM_Content}
</pre>

h2. 配置信息

<pre>
#配置企业信息号,短信机系统标识
#或者配置短信内容模板
vim app/models/msg.rb
</pre>

h2. 定时器

"whenever项目":http://github.com/javan/whenever/tree/master

<pre>
#配置定时器规则
vim config/schedule.rb
#查看定时规则
whenever
#将定时规则写入cron
whenever -w
#重启定时器让定时任务生效
sudo /etc/init.d/cron restart


#用cron执行定时任务时，会找不到oracle库（oracle安装在用户目录下,cron运行时有自己的用户），需要将下面这行
/usr/lib/oracle/10.2.0.3/client/lib/
#添加到 /etc/ld.so.conf 里并执行
sudo ldconfig -v
</pre>

h1. 参考资料

以第一为准
# "Rail wiki db support":http://wiki.rubyonrails.org/database-support/ms-sql
# "Howto_SQL2005_From_RailsOnFedora":http://www.not404.com/Howto_SQL2005_From_RailsOnFedora
# "FreeTDS排障":http://www.freetds.org/userguide/serverthere.htm#SERVERTHERE.TSQL
# "Getting Rails talking to SQLServer on OSX via ODBC":http://toolmantim.com/thoughts/getting_rails_talking_to_sqlserver_on_osx_via_odbc
注意,使用ODBC Administrator创建User DSN无法连接Datasource的话,尝试一下新增System USDN

<pre>
#如果isql -v SQL2000DSN1报以下错误
[S1000][unixODBC][FreeTDS][SQL Server]Unable to connect to data source
[01000][unixODBC][FreeTDS][SQL Server]Adaptive Server connection failed
[01000][unixODBC][FreeTDS][SQL Server]Unexpected EOF from the server
#修改/etc/odbc.ini中的TDS Version为TDS_Version(即将空格换为下划线)
</pre>
