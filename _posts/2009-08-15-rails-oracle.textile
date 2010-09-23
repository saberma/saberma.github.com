---
layout: post
title: Oracle on Rails
category: rails
---
h1. Oracle on Rails

<pre>
#如果生产环境使用Oracle，则需要以下操作
cd ~
#instance client必须
wget http://cloud.github.com/downloads/saberma/saberma.github.com/oracle-instantclient-basic_10.2.0.3-2_i386.deb
sudo dpkg -i oracle-instantclient-basic_10.2.0.3-2_i386.deb
wget http://cloud.github.com/downloads/saberma/saberma.github.com/oracle-instantclient-devel_10.2.0.3-2_i386.deb
sudo dpkg -i oracle-instantclient-devel_10.2.0.3-2_i386.deb
#非必须，sqlplus用于调试是否能正常连接
wget http://cloud.github.com/downloads/saberma/saberma.github.com/oracle-instantclient-sqlplus_10.2.0.3-2_i386.deb
sudo dpkg -i oracle-instantclient-sqlplus_10.2.0.3-2_i386.deb
#设置client路径
export LD_LIBRARY_PATH=/usr/lib/oracle/10.2.0.3/client/lib/
#设置客户端编码(重要！否则会乱码)
export NLS_LANG=AMERICAN_AMERICA.UTF8
#以上参数设置要放于全局设置文件中
echo "export LD_LIBRARY_PATH=/usr/lib/oracle/10.2.0.3/client/lib/" >> ~/.bashrc
echo "export NLS_LANG=AMERICAN_AMERICA.UTF8" >> ~/.bashrc

#测试sqlplus能连接到Oracle服务器
#sqlplus myuser/mypassword@//myserver/mydatabase.mydomain.com
sqlplus zbs_ciis/zbs@//192.168.1.29/orcl
  desc users;
  #正常的话应能返回users表结构

#安装ruby-oci8所需包
sudo apt-get install libaio1
#安装ruby-oci8，注意不能使用sudo，否则无法正常获取LD_LIBRARY_PATH参数
gem install ruby-oci8 --no-ri --no-rdoc --version 1.0.6

#注意,如果是mac平台,则安装ruby-oci8前先定义以下参数,否则mkmf.log会报
#ld: warning: in /usr/local/oracle/instantclient/libclntsh.dylib, file is not of required architecture
#Undefined symbols for architecture i386:
#  "_OCIInitialize", referenced from:
#    _OCIInitialize$non_lazy_ptr in cc1EmgZD.o
#ld: symbol(s) not found for architecture i386
export ARCHFLAGS="-arch x86_64"
#一定要注意cpu的类型是intel(32bit?64bit?)还是ppc的,下载时要区分
                                                                                                                                                             
#测试
irb
require 'rubygems'
require 'oci8'
#不报错，则表示直连正常
OCI8.new('ciis', 'ciis', '//192.168.1.29:1521/orcl')

#安装rails数据库适配器
sudo gem install saberma-activerecord-oracle_enhanced-adapter-nvarchar2 --no-ri --no-rdoc
#另外，数据库的IP地址、用户名密码等在config/database.yml中配置
</pre>

h1. 注意事项

migration中定义字段长度(:limit => 1)时，当长度等于1，oracle-adapter会将相应的实体属性类型设置为boolean
所以最好将长度设置为1以上的值

h1. 开发中的Oracle问题

1.
关联查询时的条件字段要明确指定
碰到的问题为mysql下order by id正常, Oracle报错：OCIError: ORA-00918: column ambiguously defined

2.
`表名`问题
mysql会给表名外加``(注意不是单引号),手工加条件(如排序: order by `departments`.id)时,mysql正常，Oracle报错： OCIError: ORA-00911: invalid character

3.
级联更新问题
has_one、has_many如未设置:dependent=>:delete/:delete_all，解除子项关联时，默认会去更新子项的外键为NULL，mysql下没问题，Oracle报错：OCIError: ORA-01407: cannot update

4.
日期问题
2个短格式(yyyy-mm-dd)日期(一个从数据库取)直接相减求时间差得到相差天数, mysql下正常，oracle下出错, 可以采取date.to_s(:db).to_date的办法解决

5.
修改字段
如果字段值不为空，oracle下不可修改字段类型，mysql下可以，可以用增加临时字段的方式解决。

h1. 附录

/etc/tnsnames.ora 文件内容如下:

<pre>
db29 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.1.29)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = orcl)
    )
  )
</pre>
