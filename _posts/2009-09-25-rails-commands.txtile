---
layout: post
title: Rails常用命令
category: rails
---

<pre>
# 接调用项目中的类和方法
script/console
  #查看所有的用户
  User.all
  #访问某个地址，用于调试
  app.get '/users'
  app.cookies
  app.flash
  app.request
  app.response
</pre>

<pre>
# 检查服务器后台运行情况
tail -f log/development.log
</pre>

<pre>
# 数据库迁移(test)
rake db:migrate RAILS_ENV=test
#数据库回滚3步 
rake db:migrate:redo STEP=3
</pre>

<pre>
#查看ruby环境
puts '$:'
#查看gem环境
gem environment
gem e
</pre>

h2. 在View中使用输出字符串

<pre>
#假设在haml中需要调用foo,你使用
- foo do
  hello world
#在foo中需要输出某些字符串，如saberma,则可以利用concat
def foo
  concat 'saberma'
  yield
end
</pre>

另外,capture也是个好东西:)
"capture api":http://apidock.com/rails/ActionView/Helpers/CaptureHelper/capture
"concat api":http://apidock.com/rails/ActionView/Helpers/TextHelper/concat

h2. layout中指定多个yield

"content_for":http://apidock.com/rails/ActionView/Helpers/CaptureHelper/content_for
