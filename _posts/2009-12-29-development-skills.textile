---                                                                                                                                                          
layout: post
title: 如何开始rails项目
category: rails
---

为了能胜任ruby项目的开发工作，你需要逐步掌握以下技能

h2. 1.安装操作系统(Linux)

经过实践，rails项目可以顺利在windows、linux(Ubuntu)、unix(solaris)下运行
但推荐使用的平台仍是Linux，所以你至少需要熟悉以下内容

h3. 安装

我们开发环境使用的是Ubuntu，安装后请阅读速配指南了解ubuntu的基本配置

"Ubuntu安装":http://wiki.ubuntu.org.cn/articles/i/n/s/Install_Ubuntu_desktop_8.04_a2e3.html
"Ubuntu速配指南":http://wiki.ubuntu.org.cn/Qref/Hardy

h3. 熟悉常用命令

"Linux常用命令":http://blog.csdn.net/ckboss/archive/2009/05/03/4144318.asp
"常用命令联合使用":/2009/11/23/linux.html

h3. 安装xp虚拟机

安装xp虚拟机操作系统是为了使用rtx、使用ie访问项目，具体步骤:

"安装virtualbox":http://www.virtualbox.org/wiki/Linux_Downloads
宿主操作系统为"Ubuntu 8.04 LTS",按电脑的cpu架构选择i386或者AMD的virtualbox安装包

在virtualbox安装windowns xp精简版
"ubuntu下安装xp指南":http://www.lupaworld.com/340814/viewspace-139349

h2. 2.熟悉开发工具(Vim)

经过对netbean、RadRails、Vim的使用，最终选择了Vim作为我们的IDE
vim可以让你在开发过程中尽量减少对鼠标的使用，提高编写效率

"Vim快速入门":http://blog.csdn.net/lxrm_fly/archive/2007/08/13/1741206.aspx
"Vim安装使用":/2009/10/10/vim.html

h2. 3.了解Ruby

你需要知道模块、类、继承等概念
知道循环，判断等语句
了解String,File等类的常用方法,知道API可以在哪里查询到

要不断实践ruby，irb命令是你的最重要的朋友

"二十分钟入门":http://www.javaeye.com/topic/55826
"Ruby延伸":/2009/12/18/ruby-tips.html
"Programming Ruby 1.9":http://cloud.github.com/downloads/saberma/saberma.github.com/ProgrammingRuby1.9.pdf

h2. 4.熟悉Rails

你需要知道rails的以下内容:

"项目的目录结构":http://biaoming.javaeye.com/blog/36212

rails项目中，也是遵循MVC模式的，开发一个功能时，可根据需要选择以下生成器

<pre>
#看一下有哪些生成器(Installed Generators)
script/generate
#项目中常用以下生成器
#生成控制器、实体、整合(包括控制器、视图、实体)
script/generate rspec_controller
script/generate rspec_model
script/generate rspec_scaffold
</pre>

url请求将由rails的哪些控制器中的哪些方法进行处理，需要了解

"route机制":http://blackanger.blog.51cto.com/140924/122055

出现问题时多查看后台日志
主要检查由前端页面传递至后台的参数params是否正常
并检查控制器对数据库进行操作的SQL是否正确

<pre>
#查看开发环境的日志
tail -f log/development.log
</pre>

"Rails快速入门":https://www.ibm.com/developerworks/cn/linux/l-rubyrails/
"I18n国际化":http://www.letrails.cn/archives/rails-2-2-i18n-tutorials/
"Agile Web Development":http://cloud.github.com/downloads/saberma/saberma.github.com/Agile_Web_Development_with_Rails.pdf
"Advanced Rails Recipes":http://cloud.github.com/downloads/saberma/saberma.github.com/advanced_rails_recipes.pdf

在rails项目中，要遵守［不重复发明轮子］的原则，在开发功能(非业务)前可以先找找
"Ruby工具箱":http://ruby-toolbox.com/

要不断实践rails，script/console命令是你的最重要的朋友

h2. 5.前端技术

前端技术中的视图层是采用haml框架生成的，haml代码会转换为html代码，最终在浏览器中显示
另外包含javascript,css两部分
在项目中javascript主要使用jQuery框架编写js代码
css部分使用haml中的sass编写css代码

"html入门_共8小节":http://www.21shipin.com/html/12496.shtml
"haml入门":http://haml-lang.com/tutorial.html
"javascript入门":http://www.prglab.com/blog/js-tutorial
"css入门":http://www.cainiao8.com/web/css.html
"jQuery入门":http://www.k99k.com/jQuery_getting_started.html
"jQuery常用插件":/2009/09/12/jquery-plugin.html
"jQuery最佳实践之delegate":http://jsgears.com/thread-402-1-1.html
"sass入门":http://sass-lang.com/tutorial.html

h2. 6.数据库(Mysql)

经过实践，rails项目可以连接Mysql,Oracle,Sqlserver，随时可以进行切换
在开发时，我们使用Mysql作为本地数据库，所以你至少需要掌握Mysql数据库

"Mysql入门":http://www.xingyl.com/?m=blog&id=57

h2. 7.文档数据库(NoSQL)

先看看各大NoSQL软件的对比
"NoSQL数据库探讨之一 － 为什么要用非关系数据库？":http://robbin.javaeye.com/blog/524977

"NoSQL介绍及MongoDB入门":http://renial.javaeye.com/blog/684829
"Mongodb入门":http://blog.csdn.net/lolinzhang/archive/2009/07/16/4353699.aspx

mongodb最全最好的学习资料是在官网(支持中文但很不全)，上面还有一个TRY IT OUT，可以直接在线学习简单操作

"ruby数据库驱动说明":http://www.mongodb.org/display/DOCS/Ruby+Language+Center

如果采用NoSQL，则rails中就不能再使用ActiveRecord了，需要要对应的ORM
推荐使用mongoid，虽然没有mongomapper那么功能全面，好在对rails3的支持更好(率先支持activemodel等)

mongoid也处于活跃开发期，所以最新资料只能看官网，最稳妥的资料只能看代码了
"mongoid官方文档":http://mongoid.org/docs/installation/

基于nosql的开源项目现在还比较少，可以参考刚起步的shopqi
"shopqi网店平台":http://github.com/saberma/shopqi

h2. 8.测试

为保证项目质量，开发过程中需要遵循测试驱动开发

"黄瓜测试入门":http://www.lycom.de/past/tags/cucumber
"Rspec入门":http://www.letrails.cn/archives/20/

h2. 9.版本控制Git和GitHub

以上技能掌握后，项目代码就需要进行版本控制及代码托管了

国内公司一般都是自建代码托管服务器，所用版本控制软件大多使用SVN,VSS,CVS
前两个我倒是用过，但我还是要极力推荐Git
Git原生支持分布式代码管理(KO VSS)，直观的冲突处理机制(KO SVN)

而基于Git的托管服务器Github，可以说是开源界的改革，使得开源项目真正地开源!
任何开发者可以直接获取开源代码，或追加功能或打补丁，之后只要按个按钮就可以向原作者请求合并
当然了，合并之前一般需要开发者编写好测试用例，保证代码的正确，同时不破坏原有功能

"Git使用":/2010/01/26/git.html

如果公司只有svn服务器，折衷的办法就是本地使用git，再远程提交至svn
"SVN集中存储":/2009/09/09/git-to-svn.html

h1. 其他资源

"Learn Web Design":http://thinkvitamin.com/web-design-2/learn-web-design-free-tutorial-videos/
