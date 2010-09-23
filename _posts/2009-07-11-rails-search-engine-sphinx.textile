---                                                                                                                                                          
layout: post
title: 全文检索Sphinx
category: rails
---
"参考全文检索Ferret":http://saberma.github.com/2009/07/12/rails-search-engine-ferret.html
以下内容不再使用

h1. 技术概览

* 全文检索引擎采用:sphinx(0.9.8rc2)

* 中文分词:libmmseg(0.7.3)

* Rails调用引擎的插件:thinking-sphinx(0.9.5)

注意,当前使用的thinking-sphinx与官方的不一样,增加了中文分词的配置,修正delta index无法自动更新的问题

h1. 安装

h2. libmmseg

<pre>
sudo apt-get install g++
cd ~
wget http://cloud.github.com/downloads/saberma/saberma.github.com/mmseg-0.7.3.tar.gz
tar zxvf mmseg-0.7.3.tar.gz 
cd mmseg-0.7.3 
./configure
make
sudo make install

#安装ruby扩展
cd ruby
cp /usr/local/include/mmseg/*.h .   
cp ../src/*.h .   
cp ../src/css/*.h .   
ruby extconf.lin.rb
make
sudo make install

注意:如果在这一步出错,且出错提示为：
css/UnigramCorpusReader.cpp:89: error: ’strncmp’ was not declared in this scope
则需手工编辑.src/css目录下UnigramCorpusReader.cpp 文件,在其第一行加上
#include <string.h>
然后重新 make,即可通过
</pre>
注:已生成的词库uni.lib放在项目的lib目录下(值班室项目已经放置此文件了)

h2. sphinx

<pre>
#"下载sphinx"
cd ~
wget http://cloud.github.com/downloads/saberma/saberma.github.com/sphinx-0.9.8-rc2.tar.gz
tar zxvf sphinx-0.9.8-rc2.tar.gz 
cd sphinx-0.9.8-rc2 
sudo apt-get install patch
#下载中文补丁
wget http://cloud.github.com/downloads/saberma/saberma.github.com/sphinx-0.98rc2.zhcn-support.patch 
patch -p1 < sphinx-0.98rc2.zhcn-support.patch 
#下载防crash补丁
wget http://cloud.github.com/downloads/saberma/saberma.github.com/fix-crash-in-excerpts.patch
patch -p1 < fix-crash-in-excerpts.patch 

./configure
make 
sudo make install

注意：如果在这一步出现
/usr/local/include/mmseg/freelist.h:22: error: ‘strlen’ was not declared in this scope
的错误，手工修改 /usr/local/mmseg/include/mmseg/freelist.h
在上面添加
#include <string.h>
</pre>

h2. 安装thinking-sphinx

(此步骤已经集成进［Rails说明］中［获取源代码］一节，不需要再独立执行)

<pre>
git submodule init
git submodule update
</pre>

如果安装时报错，按以下步骤处理
<pre>
#删除.gitmodules,.git/config中的submodule配置
#删除thinking-sphinx目录
git rm --cached vendor/plugins/thinking-sphinx
sudo rm -r vendor/plugins/thinking-sphinx
git submodule add -b v0.9.5chinese git://github.com/saberma/thinking-sphinx.git vendor/plugins/thinking-sphinx
</pre>

h1. 启动引擎

(此步骤应在［Rails说明］中［获取源代码］之后操作)

<pre>
#生成sphinx配置文件
rake ts:config
#建立索引
rake ts:index
#启动引擎
rake ts:start
</pre>

h1. 测试引擎

(此步骤应在［Rails说明］中［获取源代码］之后操作)

<pre>
script/console
c = Call.last
c.callnumber = '13911112222'
c.save
#可以看到后台输出更新delta index
Sphinx 0.9.8-rc2 (r1234)
Copyright (c) 2001-2008, Andrew Aksyonoff
using config file '/home/mahb/Documents/zbs/config/development.sphinx.conf'...
indexing index 'call_delta'..."防crash补丁":http://www.coreseek.com/uploads/sources/fix-crash-in-excerpts.patch 
collected 1 docs, 0.0 MB
sorted 0.0 Mhits, 100.0% done
total 1 docs, 31 bytes
total 0.031 sec, 1014.20 bytes/sec, 32.72 docs/sec
rotating indices: succesfully sent SIGHUP to searchd (pid=5812)
#开始查询
Call.search '13911112222'
#这时可以看到相应的记录
</pre>

h1. 使用说明

"参考资料":http://ts.freelancing-gods.com/usage.html
<pre>
#或参考call.rb
</pre>

h1. 相关参考资料

* "使用libmmseg实现Ruby的中文分词功能":http://www.kuqin.com/searchengine/20080525/8886.html
* "Rails程序员Sphinx中文全文检索安装指南":http://www.coreseek.com/forum/index.php?action=vthread&forum=2&topic=17
