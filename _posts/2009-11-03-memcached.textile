---
layout: post
title: memcached安装使用
category: rails
---
h1. 安装

<pre>
wget http://cloud.github.com/downloads/saberma/saberma.github.com/memcached-1.4.4.tar.gz
tar vxf memcached*.tar.gz 
cd memcached*
#这一步有问题看下一节内容
sudo ./configure
sudo make && sudo make install

#测试安装结果，有问题看下一节内容
memcached -vv
#出现一堆的调试信息表示正常,CTRL+C退出

#安装ruby客户端memcached-client
sudo gem install memcache-client --no-ri --no-rdoc
</pre>

<pre>
#configurate时遇到错误则要安装libevent：
checking for libevent directory... configure: error: libevent is required.  You can get it from http://www.monkey.org/~provos/libevent/
#安装libevent
cd ..
wget http://www.monkey.org/~provos/libevent-1.4.12-stable.tar.gz
tar vxf libevent*.tar.gz
cd libevent*
./configure && sudo make && sudo make install
cd ../memcached*
</pre>

<pre>
#memcached -vv报以下错误：
#memcached: error while loading shared libraries: libevent-1.4.so.2: cannot open shared object file: No such file or directory
#执行以下语句
sudo ln -s /usr/local/lib/libevent-1.4.so.2 /lib/
</pre>

h1. 启动

<pre>
#分配20m内存
memcached -d -m 20 -l 127.0.0.1
</pre>

h1. 测试客户端访问

<pre>
irb
require 'rubygems'
require 'memcache'

m = MemCache.new '127.0.0.1'

m.set('key','result',5)
#应该可以看缓存结果

m.get('key')
#5秒后再看，应该返回nil
m.get('key')
</pre>

h1. 在rails中使用

<pre>
#一般用法
#fetch方法会先到memcached中获取数据，获取不到时才会执行User.all生成数据并写入缓存
@list = Rails.cache.fetch("user_list"){ User.all }
#设置缓存 30分钟后失效
@list = Rails.cache.fetch("user_list", :expires_in => 30.minutes){ User.all }
</pre>

h1. 结合cache-money

cache-money是twitter.com开源的分布式缓存框架，功能强大
实现读取数据时插件自动写入缓存，数据更新删除更新缓存
默认使用id缓存，其他字段需要在model中指定要缓存的字段

<pre>
#用户一般通过User.find_by_login('saberma')方式获取
class User < ActiveRecord::Base
  index :login
end
</pre>

h2. 安装

<pre>
sudo gem install nkallen-cache-money --no-ri --no-rdoc
</pre>

"cache-money指南":http://github.com/nkallen/cache-money

h2. 配置

<pre>
#config/memcached.yml中的ttl用于设置默认过期时间(秒)，默认为1周
#cache-money中设置的过期周期默认为1天，可通过在index方法后增加ttl参数自定义
class User < ActiveRecord::Base
  index :login, :ttl => 60.minutes
end
</pre>

h2. 调试

<pre>
#可以直接通过模型类获取索引信息
#多少个索引
User.indices.size
#第一个索引的过期周期
User.indices[0].ttl
</pre>

h2. 统计

<pre>
#stats查看命中率等
telnet 127.0.0.1 11211
stats
#查看内存块分配情况
stats slabs
</pre>

h1. 参考资源

"集思博客":http://www.gisblogs.net/post/183.html
