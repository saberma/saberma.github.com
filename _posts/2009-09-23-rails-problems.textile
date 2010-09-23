---                                                                                                                                                          
layout: post
title: Rails问题集
category: rails
---
这里记录我遇到的，但在网络上无法找到解决方法的问题

h1. undefined method `alias_method_chain' for I18n::Backend::Simple:Class

在将ruby1.8.7p72换成ruby mbari 1.8.7-p72后出现的问题，问题比较隐蔽(2009-09-23)

<pre>
#运行script/console,报以下错误
/usr/local/lib/ruby/gems/1.8/gems/activerecord-2.3.2/lib/active_record/i18n_interpolation_deprecation.rb:23:NoMethodError: undefined method `alias_method_chain' for I18n::Backend::Simple:Class
</pre>

经过google,irc后，没有找到解决方法
最接近问题的就是 "这篇文章":http://shapingclouds.com/2009/06/11/solution-for-undefined-method-alias_method_chain-after-upgrading-to-rails-2-3-2/

但并不能解决问题，尝试自己解决

<pre>
#查看i18n_interpolation_deprecation源码发现根本看不出问题
#只发现加载了active_support中的i18n/backend/simple
#因此怀疑active_support加载过程中已经出错了
irb
> require 'rubygems'
> require 'activesupport'
#报以下错误
# LoadError: libruby.so.1.8: cannot open shared object file: No such file or directory - /usr/local/ruby/lib/ruby/1.8/x86_64-linux/bigdecimal.so
#果然,ldd一下
ldd /usr/local/ruby/lib/ruby/1.8/x86_64-linux/bigdecimal.so
# linux-vdso.so.1 =>  (0x00007fff647fe000)
# libruby.so.1.8 => not found
# libdl.so.2 => /lib/libdl.so.2 (0x00007f415c1d0000)
# libcrypt.so.1 => /lib/libcrypt.so.1 (0x00007f415bf97000)
# libm.so.6 => /lib/libm.so.6 (0x00007f415bd16000)
# libc.so.6 => /lib/libc.so.6 (0x00007f415b9b4000)
# /lib64/ld-linux-x86-64.so.2 (0x00007f415c5e8000)

#找一下有没有这个文件
find / -iname libruby.so.1.8
# /home/saberma/ruby-1.8.7-p72/libruby.so.1.8
# 在ruby的安装源码包中有这个文件，拷贝至加载路径
cp /home/saberma/ruby-1.8.7-p72/libruby.so.1.8 /usr/lib
# 再次运行script/console，一切顺利
</pre>
