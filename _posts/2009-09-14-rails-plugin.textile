---
layout: post
title: Rails常用插件
category: rails
---

Rails的插件很丰富，并且大部分都放在github，非常方便修改
这里简单介绍我使用过的Rails插件

h1. 实体相关

h2. restful_authentication

[必备] 生成用户实体，restful登录
"project":http://github.com/technoweenie/restful-authentication

h2. mislav-will_paginate

[必备] 分页
"project":http://github.com/mislav/will_paginate/tree/

h2. annotate_models

在实体类文件头部显示字段信息
"project":http://github.com/ctran/annotate_models/tree/

h2. paperclip

用于上传附件，并可对图片进行缩放(需要imagemagick)
可对上传的附件进行文件大小的校验
"project":http://github.com/thoughtbot/paperclip

h2. ActiveModel

[必备]将ActiveRecord实体扩展出多个临时实体
特別是实体字段非常多，各个不同阶段需要在页面更新部分字段
则可以将部分字段放在一个ActiveModel实体中，方便校验

Rails3已包含ActiveModel插件

"project":http://github.com/rails/rails/tree/master/activemodel

h1. 视图相关

h2. haml

[必备] 替换掉rails自带的erb
此插件可以使得view代码变得非常简洁
与RedCloth结合就可以使用:textile标记直接格式化了
"project":http://haml-lang.com

h2. sass

[必备] haml作者的又一力作
让css代码也变得简洁
"project":http://sass-lang.com

h2. bundle-fu

[必备] 非常易用，一行代码将js,css多个文件打包
"project":http://github.com/timcharper/bundle-fu

h2. jrails

用于将rails的prototype替换为jQuery
"project":http://ennerchi.com/projects/jrails

h2. calendar_date_select

js日期控件
"jquery support":http://github.com/ronald/calendar_date_select
"project":http://github.com/timcharper/calendar_date_select
"home":http://code.google.com/p/calendardateselect/

h2. fckeditor

富文本编辑器
"project":http://github.com/code/fckeditorp
"demo":http://ckeditor.com/demo

h2. super_inplace_controls

实现页面输入框即点即编辑
"project":http://os.flvorful.com/super_in_place_controls
"demo":http://os.flvorful.com/super_in_place_controls/demo

h2. open-flash-chart2

open-flash-chart2的rails接口
可以生成炫丽的flash图表，简单易用
"project":http://github.com/pullmonkey/open_flash_chart
"home":http://teethgrinder.co.uk/open-flash-chart-2/

h2. swf_fu

生成swfobject js，用于显示flash

"project":http://github.com/marcandre/swf_fu

h2. prawn

生成PDF，报表
中文字体最好自己下载仿宋，google simfang.ttf

<pre>
Prawn::Document.generate("bug.pdf") do
  #这一句配置才能支持中文换行，但仍不完善
  text_options.update(:wrap => :character, :size => 16)
end
</pre>
"project":http://github.com/sandal/prawn/tree/master

h2. sitemap_generator

[必备] 生成网站的sitemap

<pre>
#生成sitemap文件
rake sitemap:refresh:no_ping
</pre>
"project":http://github.com/adamsalter/sitemap_generator-plugin/

h1. 测试相关

h2. rspec

[必备] 替换rails自带的test
"project":http://rspec.info

h2. cucumber

TDD测试，用于编写可读性强、且可运行的测试用例
测试网站时依赖webrat
"project":http://cukes.info/
"code":http://github.com/aslakhellesoy/cucumber/tree/

h2. selenium-on-rails

[必备] 用于验收测试，类似于QTP
即录制操作过程各个步骤，然后回放
"project":http://seleniumhq.org/projects/on-rails/
"code":http://github.com/paytonrules/selenium-on-rails

h2. factory_girl

[必备] 替换rails的fixture
灵活构造关联性的测试数据
"project":http://github.com/thoughtbot/factory_girl/tree/master

h2. faker

用于生成随机的测试值,不支持中文
"project":http://faker.rubyforge.org/

h2. populator

结合faker，构造大数量的测试数据，用于性能测试

<pre>
#填充测试数据
rake db:populate
</pre>
"rake任务":http://gist.github.com/186485
"project":http://github.com/ryanb/populator/tree/master

h2. blue-ridge

js tdd测试，挺好用的
由于使用了rhino(a Java-based JavaScript interpreter),所以你需要安装sun-java6-jre

"project":http://github.com/relevance/blue-ridge

h1. 定时相关

h2. javan-whenever

ruby语法编写定时任务，再转成cron
"project":http://github.com/javan/whenever/tree/

h1. 部署相关

h2. capistrano

[必备] 远程部署

<pre>
#查看所有可运行的部署任务
cap -T [search]
#部署
cap deploy
#运行数据库migrate
cap deploy:migrate
#直接运行以上两步
cap deploy:migrations
</pre>
"project":http://www.capify.org

h2. deprec

用于快速搭建rails服务器生产环境
运行少量命令就帮我们安装好ruby,rails,各种依赖软件及gems
"project":http://deprec.failmode.com/

h1. 其他相关

h2. exception_logger

异常日志查看，注意只有在production环境下，且非localhost(127.0.0.1)访问时才会生成异常记录
"project":http://github.com/defunkt/exception_logger/tree/master

h2. handsoap

webservice客户端
"详细说明":/2009-12-03-ruby-soap-client.html
