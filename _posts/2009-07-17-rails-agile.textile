---                                                                                                                                                          
layout: post
title: 敏捷开发流程
categories:
  rails
  agile
---
h2. 需求采集

所有需求都采集到"PivotalTracker":http://www.pivotaltracker.com/projects/10357

h2. 实现需求

h3. 获取需求

登录"PivotalTracker":http://www.pivotaltracker.com/projects/10357
在Icebox中找到分值（优先级较高）的故事，点击Start按钮

h3. 开启后台测试监控

<pre>
#这句也可放在.bashrc中，就不用自己打了
export AUTOFEATURE=true
script/autospec
</pre>

h3. 定义用户行为（故事）

<pre>
script/generate feature user name:string
      #执行结果如下，这样就生成了feature文件和相应的step文件
      exists  features/step_definitions
      create  features/manage_users.feature
      create  features/step_definitions/user_steps.rb
#使用vim编辑feature文件
vim features/manage_users.feature
</pre>
cucumber支持中文描述用户行为，
可通过以下查询中文关键字与英语关键字的对应关系

<pre>
cat /usr/lib/ruby/gems/1.8/gems/aslakhellesoy-cucumber-0.1.99.22/lib/cucumber/languages.yml | grep -A 13 zh-CN
</pre>

不得不说一下cucumber的step定义,比如有以下feature部分定义

<pre>
场景: 用户登录
  假如 我在登录页面
  当 我输入用户名为admin
</pre>

则step定义可以是这样的

<pre>
当 /我输入(.*)为(.*)/ do |label, value|
  #以下语句可用于调试,显示页面内容
  #puts response.body
  fill_in(label, :with => value)
end   
</pre>

页面源码是这样的

<pre>
<code>
<label for='login'>用户名</label>
<input id="login" is_focus="true" name="login" type="text" />
</code>
</pre>

即fill_in方法会自动去找页面中<label for='login'>用户名</label>对应的输入框,并且将值输入,

<pre>
#单独运行feature中的某个scenario
cucumber --language zh-CN features/manage_calling_events.feature -s 更新来电
#查看test日志,以便跟踪测试过程出现的问题
tail -f log/test.log
</pre>

h3. 编写Rspec单元测试代码

<pre>
#生成实体
script/generate rspec_model event phone:string timing:datetime
#打开event_spec.rb编写测试代码

#测试的一般内容为:
#测试是否能够正常保存(加感叹号表示出现错误时抛异常)
Event.create!(@valid_attributes)
#测试记录数变化情况
lambda do
  Call.create!(@valid_attributes)
end.should change(Case, :count).by(1)

#测试数据的准备,所有实体都有对应的fixture,文件名如cases.yml
noise:
  content: 楼下太吵了

#在rspec中调用测试记录
#先在describe Call do下一行定义
fixtures :cases 
#之后就可以这样调用了
cases(:noise)
</pre>

h2. 验收测试

"安装selenium firefox插件":https://addons.mozilla.org/zh-CN/firefox/downloads/latest/2079/addon-2079-latest.xpi
"安装rselenese格式(即将rselenese.js的内容粘贴至IDE中)":http://wiki.openqa.org/display/SIDE/SeleniumOnRails

<pre>
#rselenese格式安装详细说明
#在firefox菜单中打开[Tools]-[Selenium IDE]
#在Selenium IDE菜单中打开[Options]-[Options...]-[Formats]
#点击左下角的[Add]
#在Name of the format:中输入rsel
#将下载的rselenese.js的内容粘贴到文本框中
#点击右下角的[OK]按钮保存，退出
</pre>

<pre>
#每次手动测试时，都打开firefox菜单Tools=>Selenium IDE
#录制测试脚本后，以rsel格式保存至spec/selenium/中的某个子目录(如admin)
#运行以下语句，系统打开浏览器，自动运行测试脚本
mongrel_rails start -e test -d
rake test:acceptance
#注意，如果测试过程需要修改程序代码，则修改后要重启服务器，修改rsel除外
mongrel_rails restart
</pre>

h2. 代码覆盖率

h3. 应经常查看测试代码的覆盖情况

<pre>
#安装rcov
sudo gem install spicycode-rcov --no-ri --no-rdoc
#运行rspec rake
rake spec:rcov
#查看报告
firefox coverage/index.html
#之后就补充测试代码了
</pre>

h2. 常用测试策略

# 数据库记录正常保存,则记录数会加1

h2. 结对编程

利用ssh,screen,vim实现结对编程

<pre>
#screen配置文件，以支持256color
wget http://saberma.github.com/file/dot/screenrc -O ~/.screenrc

#安装autossh，避免ssh闲时等待过长自动断开连接
sudo apt-get install autossh
#下载rscreen脚本,调用autossh登录后直接运行screen
wget http://saberma.github.com/file/bin/rscreen -O /usr/local/bin/rscreen
sudo chmod a+x /usr/local/bin/rscreen

#测试一下
screen -xR
vim
#另开一个terminal
rscreen localhost
#会看到两个terminal显示相同vim
</pre>

h2. 其他

在测试用例,单元测试都通过,而手动测试出现错误的时候,一定不能直接修正
应该先补充测试,让测试失败,再修正问题

出现疑难问题时，为便于相互协作，可通过ssh远程连至问题机后直接操作

<pre>
#问题机安装ssh服务
sudo apt-get install openssh-server
#其他机连接问题机(ip为191,用户名为saberma)
ssh 192.168.1.191 -l saberma
#输入密码
</pre>

h1. 参考资料

"Selenium Reference":http://release.seleniumhq.org/selenium-core/0.8.0/reference.html
"Selectors":http://www.w3.org/TR/2001/CR-css3-selectors-20011113/

