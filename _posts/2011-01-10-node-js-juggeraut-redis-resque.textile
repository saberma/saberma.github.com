---                                                                                                                                                          
layout: post
title: 应用node.js,redis,resque构建实时项目
category: rails
#published: false
---

我对javascript语言还是很喜欢，所以对node.js这么有创意的东西，很早就开始关注了。
据说在实时领域的表现非常好，资源占用也不多，很想实践一番，以后正式项目有对实时性的需求，就可以放心地使用node.js了。

所以我决定将 "55true":https://github.com/saberma/55true 重新开发，加入即时问答的机制，普通功能开发上使用rails3，而实时性上应用node.js。
虽然node.js相关的框架已经非常多，也有类似于rails的MVC框架，但是毕竟发展时间还不长，所以使用rails结合node.js的方式进行开发是个不错的发展方式，各取所长。

h2. Node.js

!/images/article/redis/nodejs.png!

node.js是一种可以用js来编写的服务端程序

这次使用 "https://github.com/maccman/juggernaut":https://github.com/maccman/juggernaut 框架，结合redis，实现rails与node.js服务的交互。
node.js的发展非常的快，有类似于rails bundler的插件管理工具npm，但为降低node.js相关框架的管理复杂度，我直接将juggernaut及其依赖的js文件都放在 "55true":https://github.com/saberma/55true 的根目录。

h3. 安装

pre. 
cd ~/Downloads
wget http://nodejs.org/dist/node-v0.2.6.tar.gz
tar -xzvf node-v0.2.6.tar.gz
cd node-v0.2.4
./configure
make
make install

h3. Juggernaut

"Juggernaut":https://github.com/maccman/juggernaut 是包含服务端及客户端的实时解决方案，最初服务端是用ruby eventmachine实现的。
node.js出来后，整个框架就用node.js重写了

h3. 启动Juggeranut

pre. 
#node server.js
sudo node server.js

使用root帐号启动，否则firefox客户端访问会报错

bq. 
Flash is optional, but it's the default fallback for Firefox (until the beta is released). Start the server using root if you want Flash support. It needs to open a restricted por

h2. Redis

!/images/article/redis/redis.png!

redis是类似于memcached的高性能键值缓存工具，但它支持更多的值类型、功能更加丰富，且可以将存储内容定时持久化到磁盘中，是NoSQL家庭中出色的一员

h3. 安装

pre. 
wget https://github.com/antirez/redis/tarball/2.2.0-rc2
tar -xzvf antirez-redis-2.2.0-rc2-0-g0540df2.tar.gz
cd antirez-redis-b703b5d
make
sudo make install

h3. 客户端

pre. 
redis-cli

h3. 调试

运行客户端后，输入命令

pre. 
monitor

h3. 注意expire

redis的expire命令有限制，执行此命令后，对key进行的任何操作都将先对key进行清空操作，详见 "http://redis.io/topics/expire":http://redis.io/topics/expire
2.1.3以上版本不存在这个限制，但未正式发布稳定版，2.1正式发布时会更改为2.2版本

具体表现如下:

bq. 
redis> set a 100
OK
redis> expire a 600
(integer) 1
redis> incr a
(integer) 1
redis> get a
"1" #正常来说，应返回101，因为还key还没有过期

如果对这个限制敏感，可以尝试 "2.2.0rc2版本":http://redis.io/download
@make@ 之后最好再执行下 @make test@ 测试是否正常，我试过，确实不再存在上面说的奇怪表现

h3. redis-rb

redis支持多语言实现的客户端访问，redis-rb是redis的客户端之一，基于ruby语言实现。
redis-rb中的方法名称与redis的一致

h2. Resque

!/images/article/redis/resque.png!

resque是基于redis的后台任务组件，能把任何类或模块作为任务在后台执行，且自带前台管理功能，方便查看执行情况。

h3. 使用

1. 编写任务

pre. 
class WorkerClass
  @queue = "demo"
  def self.perform(args)
    sleep 3 
    puts "Doing something complex with  #{args}"
  end
end

2. 进入任务队列

pre. 
require "resque"
Resque.enqueue(WorkerClass, args)

3. 运行任务

h3. 与rails3整合

0. 配置redis
新增 @config/redis.yml@ 文件，加入以下内容

<pre>
defaults: &defaults
  host: localhost
  port: 6379

development:
  <<: *defaults

test:
  <<: *defaults

staging:
  <<: *defaults

production:
  <<: *defaults
</pre>

1. 加载resque

pre. 
echo "require 'resque'" > config/initializers/load_resque.rb
config = YAML::load(File.open("#{Rails.root}/config/redis.yml"))[Rails.env]
Resque.redis = Redis.new(:host => config['host'], :port => config['port'])

2. 由于worker类都放在app/jobs，因此需要指定rails加载此目录
修改 @config/application.rb@，加入以下内容

bq. 
config.autoload_paths += %W(#{config.root}/app/jobs)

3. 加入rake任务
修改 @RakeFile@，加入以下内容

bq. 
require 'resque/tasks'
task "resque:setup" => :environment

第二句指明运行 @resque:setup@ 前先初始化rails环境


h3. 运行resque后台任务

pre. 
QUEUE=* rake resque:work

h3. 查看任务执行情况

pre. 
#运行resque前台管理服务器
resque-web -p 8282

打开浏览器，访问 "http://0.0.0.0:8282":http://0.0.0.0:8282
前台可以查看失败的worker及其日志，可以手动执行retry操作

h3. 定时任务

如需定时执行任务，可以使用 "resque-scheduler":https://github.com/bvandenbos/resque-scheduler

h3. 测试

在测试的时候我们不需要测试过程与resque是异步的,否则resque执行的时候数据可能已经被清空了,使用resque_spec插件可以让resque worker立即执行

详情查看 "https://github.com/leshill/resque_spec":https://github.com/leshill/resque_spec

h2. 参考资源

"Resque commands":http://redis.io/commands
"Resque github repository":http://github.com/defunkt/resque
"Resque with redis to go":http://blog.redistogo.com/2010/07/26/resque-with-redis-to-go/
"Do you know resque":http://rubylearning.com/blog/2010/11/08/do-you-know-resque/
"resque-scheduler, resque, rails integration, redis":http://blog.llamarada.net/?p=2202
