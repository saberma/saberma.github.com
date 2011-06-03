---                                                                                                                                                          
layout: post
title: 部署rails项目到heroku，并使用mongohq文档数据库
category: rails
#published: false
---

h2. 简介

h3. Heroku

!/images/article/heroku/heroku.png!

heroku作为rails项目的托管商，具有功能完备、操作简单等优点，而且提供免费服务
免费项目只提供一个应用服务进程(即只同时在线1个)，提供5m免费关系型数据库

h3. MongoHQ

!/images/article/heroku/mongohq.png!

mongohq提供mongodb文档数据库的在线存储服务
免费帐号每个数据库有16m的存储空间，heroku已经支持mongohq，将之纳入addons中

bq. 
因此，想要把迷你型或实验性的项目放到网上，又想免费，heroku是最好的选择了

这几天想要查找域名，需要批量查找5-8位拼音组合的.com域名(四位的已绝迹)
所以写了个程序批量查找有效域名，放到heroku上。

h2. 安装heroku

pre. 
gem install heroku --no-ri -no-rdoc

h3. 安装其他

rails3及mongodb等环境的安装，可参考 "http://github.com/saberma/shopqi":http://github.com/saberma/shopqi

h2. 新增项目

h3. 新增rails项目

pre. 
rails new test_heroku
cd test_heroku
#修改GemFile，加入必要的Gem
#...
#生成mongoid配置文件
rails g mongoid:config
git init
git add .
git commit -m "new app"

h3. 新建heroku app

bq. 
先到 "heroku.com":heroku.com 注册帐号

pre. 
heroku create test_heroku
#按提示输入注册时的帐号和密码

h3. 加入MongoHQ Addon

pre. 
heroku addons:add mongohq:free
#查看新增的数据库信息(MONGOHQ_URL部分)
heroku config --long

修改 @config/mongoid.yml@, production只留以下内容

pre. 
production:
  uri: <%= ENV['MONGOHQ_URL'] %>

*mongoid已经支持uri参数了，不需要像mongohq官方文档描述的那样，增加config/initialize/mongo.rb文件*

h3. 部署至heroku

pre. 
git push heroku master

h3. 测试

浏览器访问 "http://test_heroku.heroku.com":http://test_heroku.heroku.com ，看是否显示rails的信息

h2. 在MongoHQ中查看数据

# 登录 "MongoHQ":https://mongohq.com/databases
# 点击右边栏按钮"Add a Remote Connection"
# 输入Name和URI，确定

bq. 
URI为heroku中通过 @heroku config --long@ 查看到的MONGOHQ_URL内容

h2. heroku常用命令

pre. 
# 运行rake
heroku rake db:seed
# 运行控制台
# 查看运行进行
heroku ps
# 查看日志
heroku logs
# 查看参数
heroku config --long
# 重命名(子域名)
heroku rename newname
heroku console

h2. 参考资源

"Heroku Docs":http://docs.heroku.com/
"MongoHQ Heroku Addon":http://docs.mongohq.com/ruby-heroku-addon
"Batch Search Domains":http://github.com/nbrochu/bulkdom
"Fcitx Phrase Pinyin":http://vimim.googlecode.com/files/fcitx.phrase.pinyin.txt
"Pinyin Frequence":http://open-phrase.googlecode.com/files/phrase_pinyin_freq_sc_20090402.txt.bz2
