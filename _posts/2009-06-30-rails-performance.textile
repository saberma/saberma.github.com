---                                                                                                                                                          
layout: post
title: 性能分析
category: rails
---
h2. 异常分析

采用exception_logger记录异常信息
在production环境下，可以访问/exceptions查看异常

exception_logger需要will_paginate,RedCloth gems

h2. 性能分析

h3. 服务器IO性能

<pre>
#使用iostat进行分析
sudo apt-get install sysstat
iostat -x 1 10
</pre>

<pre>
#查看磁盘剩余空间
df -h
#查看当前目录大小
du -ch --max-depth=1
</pre>

h3. Web端分析

分析哪些action经常被访问等方面
使用request_log_analyzer gem

<pre>
#查看分析报表
request_log_analyzer log/production.log
</pre>

h3. 内存使用情况

<pre>
#查看内存剩余
free -m
#当前内存详情
cat /proc/meminfo
</pre>

h3. 数据库分析

<pre>
#利用mytop查看mysql负载
sudo apt-get install mytop
mytop -uroot -p
</pre>

h1. 参考资料

# "iostat来对linux硬盘IO性能进行了解":http://www.php-oa.com/2009/02/03/iostat.html
# "网站优化之系统瓶颈查找":http://qianjigui.javaeye.com/blog/387070

"install the oracle instant client on Mac OS X":http://www.pixellatedvisions.com/2009/03/25/rails-on-oracle-part-1-installing-the-oracle-instant-client-on-mac-os-x
