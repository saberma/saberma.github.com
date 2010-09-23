---                                                                                                                                                          
layout: post
title: 全文检索Ferret
category: rails
---
h1. 全文检索(Ferret)                                                                                                                                         

由于sphinx只能支持mysql数据库，所以只能采用其他全文检索方案了
(sphinx新版本0.9.9rc2是支持odbc连接oracle的，但coreseek的中文分词补丁只支持0.9.9rc1，而且thinking-sphinx也不支持oracle-adapter)

* 全文检索引擎采用:ferret
* 中文分词:rmmseg
* Rails调用引擎的插件:act_as_ferret

h1. 项目下安装

<pre>
#安装gem 
sudo gem install ferret acts_as_ferret rmmseg --no-ri --no-rdoc
#成功运行上面命令之后，接下来的内容可以不用操作了
</pre>

h1. 安装详细过程

<pre>
#测试
echo "敏捷开发流程" | rmmseg
#修改environment.rb
#在enviroment.rb中插入
config.gem 'acts_as_ferret'
config_gem 'rmmseg'
require 'rmmseg/ferret'
#生成ferret_server运行脚本
aaf_install
#运行生产环境下的ferret服务器，避免多线程同步访问索引的问题
script/ferret_server start --root=. -e production
</pre>

h1. 使用

<pre>
#进入控制台
script/console
#查询
Conversation.find_with_ferret '26741022'
#通过fixture加载数据不会被索引，因此，加载后应重建索引
Conversation.rebuild_index
#与查询
Conversation.find_with_ferret '26741022 深圳'
#或查询(OR大写)
Conversation.find_with_ferret '26741022 OR 深圳'
</pre>

h1. 其他

<pre>
#查看日志
tail -f log/acts_as_ferret.log
#查看索引(目录test可以替换为其他环境,如development)
ferret-browser index/test/conversation/
#然后在浏览器中访问http://localhost:3301
</pre>

h1. 参考资料

"ferret+acts_as_ferret+RMMSeg全文索引+中文分词的使用":http://wtb.javaeye.com/blog/185419
"rmmseg homepage":http://rmmseg.rubyforge.org/
"acts_as_ferret中文教程[Rails全文检索插件]":http://hi.baidu.com/%D0%C7203/blog/item/1ab899887805d197a5c2725f.html

