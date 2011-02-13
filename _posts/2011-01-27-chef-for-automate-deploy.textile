---                                                                                                                                                          
layout: post
title: 应用chef构建服务器集群自动化部署与管理
category: linux
#published: false
---

为了将rails程序部署到服务器上，需要做的工作有

# 安装服务器各组件，如libcurl等
# 配置服务器，如用户管理、iptable等
# 安装数据库
# 安装缓存服务
# 安装配置前端应用服务器
# 安装rvm
# 安装项目所需gems

以上工作属于新服务器到位后的一次性工作，而项目更新升级的工作属于不定期重复性工作，大致如下：

# 下载最新源代码
# 运行数据库脚本
# 重新启动应用服务器

此外，日常工作还包括服务器的监控、维护等

当项目规模不大，服务器只有一两台时，可以手动或者应用 "Capistrano":https://github.com/capistrano/capistrano 及 "deprec":https://github.com/mbailey/deprec 自动处理

h3. capistrano的缺点

在应用capify安装某些组件的过程中，如果出现问题，只能进入源代码检查相应的recipe，因为capistrano的recipe相对来说是隐性的
比如，安装之前，我们不知道mysql的安装会是通过package直接安装还是通过下载source本地编译后安装，也无法获知其版本
而当recipe不符合你的要求时，需要对其进行改造的成本比较大

随着项目规模的不断扩大，比如，有了专门的数据库服务器，甚至是数据库服务器集群，这时候capistrano就会显示力不从心了
我们需要有专业的服务器管理配置工具来统一管理所有的服务器，这类工具不少，这里只介绍chef

h2. Chef

h3. Chef是一个什么样的工具

想像一下我们现在需要搭建一台mysql database slave服务器，安装过程我们手动操作了
没过多久，我们需要第二台，这时候我们会想，如果之后安装第一台的时候把操作过程执行的命令写成脚本
现在安装第二台，运行一下脚本就行了，节约时间而且不容易出错

chef就相当于这样的一个脚本管理工具，但功能要强大得多，可定制性强
chef将脚本命令代码化，定制时只需要修改代码，安装的过程就是执行代码的过程

*架构图*

!/images/article/chef/chef.png!

h3. Chef的三种管理模式

1. Chef-Solo

由一台普通电脑控制所有的服务器，不需要专设一台chef-server

2. Client-Server

所有的服务器作为chef-client，统一由chef-server进行管理，管理包括安装、配置等工作
chef-server可以自建，但安装的东西较多，由于使用solr作为全文搜索引擎，还需要安装java

3. Opscode Platform

类似于Client-Server，只是Server端不需要自建，而是采用 "http://www.opscode.com":http://www.opscode.com 提供的chef-server服务，本文描述以此方式为主，免费帐号可以管理5个服务器

!/images/article/chef/opscode.png!

h3. Library程序库

程序库是定义的module方法，可以在chef中任何地方被调用，在方法体内可以执行与数据库等资源的交互动作
详见 "Libraries":http://wiki.opscode.com/display/chef/Libraries

h3. 操作对象

chef可以通过recipe指定新建目录、生成配置文件、安装gems等操作，可控性非常强

*新建目录*

新建目录/data，owner为node[:user]指定的内容，权限代码为0755

bq. 
directory "/data" do
　owner node[:user]
　mode 0755
end

*生成配置文件*

以someservice.conf.erb为模板，生成/data/someservice.conf配置文件，生成时会传递参数applications给模板

bq. 
template "/data/someservice.conf" do
　owner node[:user]
　mode 0644
　source "someservice.conf.erb" 
　variables({
　　:applications => node[:apps]
　})
end

h2. 把一台新的vps纳入chef管理

假设vps ip为188.188.188.188
ssh密码为123456

h3. 注册

到 "https://cookbooks.opscode.com/users/new":https://cookbooks.opscode.com/users/new 注册帐号
通过邮件认证后，进入控制台 "https://manage.opscode.com":https://manage.opscode.com 创建organization
按提示下载两个链接文件至~/Downloads: Download validation key | Generate knife config
点击右上角的用户名，下载链接文件至~/Download: Get a new private key

下载后的knife.txt要改名为knife.rb
*以上三个文件请妥善保管，下载之后opscode平台不再保存这些文件*

h3. 本地安装

pre. 
gem install chef net-ssh-multi --no-ri --no-rdoc
cd ~/Documents
git clone git://github.com/opscode/chef-repo.git
cd chef-repo
mkdir -p .chef

pre. 
# USERNAME和ORGANIZATION改为实际的文件名称
cd ~/Downloads
cp USERNAME.pem ~/Documents/chef-repo/.chef
cp ORGANIZATION-validator.pem ~/Documents/chef-repo/.chef
cp knife.rb ~/Documents/chef-repo/.chef

pre. 
#测试连接opscode platform
knife client list
#成功连接则显示validator的数组，如
#[ "shopqi-validator" ]

h3. 为vps安装ruby及chef客户端环境

pre. 
knife bootstrap 188.188.188.188 -u root -P 123456
#ssh登录服务器不再需要输入密码
ssh root@188.188.188.188 'sh -c "mkdir ~/.ssh"'
scp ~/.ssh/id_rsa.pub root@188.188.188.188:/root/.ssh/authorized_keys

h3. 远程测试是否安装正常

pre. 
ssh root@188.188.188.188
chef-client
#如果出现错误，可使用调试模式
chef-client -l debug

h2. 让vps运行第一个cookbook

安装完服务器操作系统后，第一件事情就是更新软件
一般情况下，我们会在服务器上运行命令

pre. 
sudo apt-get update

现在我们要通过chef来指定vps运行此命令

h3. 本地安装apt cookbook

pre. 
#此命令最常用，会从opscode官网 "http://cookbooks.opscode.com/":http://cookbooks.opscode.com/ 下载cookbook
knife cookbook site vendor apt

为node加入recipe

pre. 
knife node list
#以上查看node id，假如为201166，现在查看node的run_list
knife node show 201166 -r
#加入apt recipe到vps的run list中
knife node run_list add 201166 "recipe[apt]"
knife node show 201166 -r
knife cookbook upload -a

h3. 远程登录vps，开始按run_list运行指定recipe

pre. 
ssh root@188.188.188.188
chef-client
#每次本地更新后都要在远程运行chef-client比较麻烦，可以使用以下命令，设置为后台程序运行
chef-client -i 3600 -s 600

h2. 安装ruby-on-rails3

h3. 下载cookbook

pre. 
#参数-d要求下载依赖的cookbook
knife cookbook site vendor rvm -d
#此cookbook调用了chef内置的deploy resource，实现类似于capistrano的部署功能
#但此cookbook还依赖了java cookbook等，忽略它们即可
knife cookbook site vendor application -d

h3. 新增apps data bag

data bag相当于全局变量，application cookcook需要根据apps指定role,recipe等参数

pre. 
export EDITOR=vim
#参考 "https://github.com/opscode/cookbooks/tree/master/application":https://github.com/opscode/cookbooks/tree/master/application 将json粘贴进来
#退出vim时，data bag会被自动上传至chef server，本地不保存
knife data bag create apps 55true
#保存至本地
mkdir data_bags/apps
knife data bag show apps 55true > data_bags/apps/55true.json
knife data bag from file apps 55true.json

h3. 新增role rails

pre. 
export EDITOR=vim
knife role create rails

h3. Tip

服务器集群中可能会区分出production, staging环境，也可能同一应用部署在多台服务器中，但只需由其中的一台服务器运行数据库迁徙脚本

# 新增production, staging等role，设置app_environment attribute，分配至相应的node，这样可以重复使用deploy cookbook
# 新增app_name_run_migrations role，设置run_migrations attribute

h2. 常用cookbook

以下列出在 "https://github.com/opscode/cookbooks":https://github.com/opscode/cookbooks 中常用的cookbook

h3. apt

此recipt会运行apt-get update，用于更新操作系统

h3. runit

用于保证unicorn等服务时刻处于运行状态
runsvdir服务会不停地监测/etc/sv目录下的服务目录，每个目录对应一个runsv服务
如果某个runsv服务出现故障停止了,runsvdir会自动重新启动它

pre. 
#sv服务不启动时查看日志
ps -ef | grep runsvdir
#查看服务的状态
sv stat unicorn_server
#查看服务的日志
tail -f /etc/sv/unicorn_server/log/main/current

h3. bluepill

runit保证了服务的运行，bluepill保证进程的cpu、memory占用率处于正常水平

h3. users

新增用户，用户定义在users data bag中

h3. mongodb

安装1.6.5版本的mongo server
"https://github.com/papercavalier/mongodb-cookbook":https://github.com/papercavalier/mongodb-cookbook

h3. unicorn

经过对比，还是选择37signal的unicorn cookbook，因为支持多app部署，而unicornapp不支持(opscode官方已经删除此cookbook)。为了对unicorn子线程进行监控，两者都使用了bluepill取代God进行监控。
注意：在production环境下，rails3默认不会处理public目录下静态文件的请求，所以不能像dev环境下直接通过指定端口访问某个unicorn服务

h3. application

-这个包含太多的依赖cookbook了，经过以上的实践，还是觉得不用它了，因为里面对rails项目有用的就是deploy部分，自己重写一个也很容易-

h2. 注意事项

在service或者definition中传递参数时，如果将父params需要级联传递下去，得手动为params赋值至其他变量，因为在service或者definition块中，params参数已经被当前块覆盖了
详情参考："http://tickets.opscode.com/browse/CHEF-422"

h2. 参考资源

"Infrastructure Automation with Chef(Read This First)":http://www.slideshare.net/adamhjk/infrastructure-automation-with-chef
"Getting started with Chef tutorial":http://www.themomorohoax.com/2010/07/31/ruby-chef-tutorial
"Getting Started With The Opscode Chef Platform – Configuration Management In The Cloud":http://www.agileweboperations.com/getting-started-with-the-opscode-chef-platform-configuration-management-in-the-cloud
"How to Deploy Ruby on Rails With The Opscode Chef Application Cookbook":http://www.agileweboperations.com/how-to-deploy-ruby-on-rails-with-the-opscode-chef-application-cookbook
"Cooking with Chef 101":http://brainspl.at/articles/2009/01/31/cooking-with-chef-101
"Why Startups Need Automated Infrastructures":http://www.slideshare.net/adamhjk/why-startups-need-automated-infrastructures
"Deploy Resource":http://wiki.opscode.com/display/chef/Deploy+Resource
"application cookbook":https://github.com/opscode/cookbooks/tree/master/application

"runit":http://smarden.org/runit/
