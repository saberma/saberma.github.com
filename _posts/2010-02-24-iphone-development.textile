---
layout: post
category: iphone
title: iphone开发
---
h2. 开发目标

尝试在mac开发iphone应用，在暂时没拿到apple license前使用(linux)ToolChain编译到越狱后的iphone上测试
实践结果表示，ToolChain可以稳定的部署至已越狱的iphone上，如果报错(某个实例无法响应某方法)，一般是由于编译时未包含所有的m文件(OBJS变量未包含所有m文件所在目录)

h2. 搭建开发环境

官方SDK开发出的软件需要由苹果进行认证后才能在iphone上安装，费用为99美元，并可在AppStore上销售，苹果提成30%，且开发环境只能在苹果机上搭建
非官方SDK无须认证，不用交费，开发环境可在任何操作系统进行，缺点是开发过程较为烦琐

支持3.1
"iphonedevonlinux":http://code.google.com/p/iphonedevonlinux/wiki/Installation

-支持2.2-
-"install-open-toolchain-on-ubuntu":http://pimpmiphone.info/howto/install-open-toolchain-on-ubuntu.html-
-iptc.sh报错，修改appIdKey，增加--no-check-certificate参数后的版本-
-"iptc.sh":http://gist.github.com/raw/314175/422ce51bfad557af9ea55a45e3acf3451edfa46a/iptc.sh-

-使用iptc.sh可以不看以下内容-
-"安装toolchain非官方SDK":http://code.google.com/p/iphone-dev/wiki/Building-

h2. Mac机上的iphone开发

注意：模拟器上不支持摄相头，只能通过程序自动判断在模拟器上使用照片浏览，而在iphone上使用camera

-"应用CoreData持久化数据的例子":http://developer.apple.com/iphone/library/documentation/DataManagement/Conceptual/iPhoneCoreData01/Introduction/Introduction.html-
不使用CoreData，改用SqlitePersistentObjects，见常用框架

"调用摄像头照相的例子":http://developer.apple.com/iphone/library/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedFeatures/AdvancedFeatures.html#//apple_ref/doc/uid/TP40007072-CH14-SW13
"iphone simulator不支持camera":http://developer.apple.com/iphone/library/documentation/Xcode/Conceptual/iphone_development/200-iPhone_Development_FAQ/faq.html

h2. 问题

h3. ToolChain编译时找不到Foundation/Foundation.h中的类，如NSData

需要在每个报错的source文件中手动加入

<pre>
#import <Foundation/Foundation.h>
</pre>

后来发现原来是没有引入Prefix.pch预编译头文件
在编译时加入以下参数，问题解决

<pre>
CFLAGS += -include ./*_Prefix.pch
CFLAGS += -x objective-c-header
</pre>

h3. 如何在编译时引入libsqlite.3.0.dylib

<pre>
#LDFLAGS中加入参数
-lsqlite3.0
</pre>

h3. ToolChain编译时找不到项目中依赖的h文件

暂时没能找到自动查找所有递归子目录的方法，笨拙的办法是在编译时手工加上-Idir参数

<pre>
CFLAGS += -I./Classes/ObjectiveResource
CFLAGS += -I./Classes/SQLPO/
#有多少子目录就加多少个-I参数
</pre>

h3. ToolChain在链接成可执行文件时报错

<pre>
Undefined symbols:
  "_main", referenced from:
        __start in crt1.o
ld: symbol(s) not found
collect2: ld returned 1 exit status
make: *** [HelloToolChain] Error 1
</pre>

提示内容是，在编译后的o文件中找不到main方法

经过多次尝试，发现是由于编译main.m时带上了参数

<pre>
CFLAGS += -include ./*_Prefix.pch
CFLAGS += -x objective-c-header
</pre>

针对main.m编译时去掉这两个参数后再进行链接就ok了

为了由MakeFile自动处理main.m的编译，可以加入以下make target，即加入CFLAGS参数

<pre>
main.o: main.m
  $(CC) -c $< -o $@
</pre>

h3. 如何处理plist文件中的变量($(PRODUCT_NAME)等)

<pre>
#使用sed替换
cat $(PROJECTNAME)-Info.plist | sed 's/$${PRODUCT_NAME.*}/$(PROJECTNAME)/' | sed 's/$${EXECUTABLE_NAME}/$(PROJECTNAME)/' >  $(BUILDDIR)/$(APPFOLDER)/Info.plist
</pre>

h3. ssh上传文件至iphone后无法显示图标

这个问题比较奇怪，删除iphone.app目录，再kill SpringBoard后会显示出来

h3. 在iphone上运行直接跳出

ssh到iphone的应用目录，直接使用./iphone_运行，报以下错误

<pre>
2010-03-24 17:04:00.023 iphone_[1281:903] *** Assertion failure in UIApplicationMain(), /SourceCache/UIKit/UIKit-984.38/UIApplication.m:1069
2010-03-24 17:04:00.038 iphone_[1281:903] *** Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'Unable to instantiate the UIApplication delegate instance. No class named iphoneAppDelegate is loaded.'
2010-03-24 17:04:00.063 iphone_[1281:903] Stack: (
  853417245,
  845594132,
  852966195,
  872039017,
  817832324,
  8872,
  8572,
  8216
)
terminate called after throwing an instance of 'NSException'
Abort trap
</pre>

第一个原因是MakeFile中的OBJS没有加入Classes目录下的.m文件，补上以下内容问题解决:

<pre>
OBJS+=$(patsubst %.m,%.o,$(wildcard $(SRCDIR)/*.m))
</pre>

还是报相同的错，在注释以下语句后问题解决

<pre>
#CFLAGS += -x objective-c-header
</pre>

h2. debug iphone

将应用部署至iphone真机后，出问题时只能通过日志记录的方法进行调试了
方法是打开syslogd守护进程，将NSLog的内容重定向至系统日志中

h2. 常用开源框架

"sqlitepersistentobjects(activerecord)":http://code.google.com/p/sqlitepersistentobjects/
"object resource(activeresource)":http://iphoneonrails.com/

"cocos2d":http://www.cocos2d-iphone.org
cocos2d是开发游戏必备框架，支持物理引擎，碰撞检测

"json framework(已包含进object resource)":http://code.google.com/p/json-framework/
如需支持上传照片，需要将照片编码为base64字符后使用json上传，具体代码如下
<pre>
NSData *photoData = UIImagePNGRepresentation(fragment);
NSString* photoString = [photoData base64Encoding];
</pre>

h2. iTunes Connect

注意上传binary时要保证Icon.png的大小为57X57pixel
使用xcode编译时要选择Device Distribution编译,使用模拟器编译上传时会提示签名无效

h2. 制作iphone app site

在heroku中新增app
加入Custom Domains指定域名,使用Zerigo DNS作为域名解释服务器(即godaddy中的nameserver指向zerigo)
"应用cu3er制作幻灯片":http://www.progressivered.com/cu3er/

h2. 参考资源

"易飞扬博客":http://www.yifeiyang.net/category/embedded/iphone-embedded/
"iphone faq":http://www.iphonedevfaq.com/index.php?title=Main_Page
"How to enable Syslog on iPhone fw 2.x for debugging":http://www.fosk.it/how-to-enable-syslog-on-iphone-fw-2x-for-debugging.html
"iphone圆角图标制作":http://www.joyiphone.net/MySpace/Icon/PhotoUpload.aspx
"iPhone Simulator Cropper":http://www.curioustimes.de/iphonesimulatorcropper/
"SimCap(截图推荐,支持ipad)":http://www.jaml.co.uk/simcap.html
"how to create debug flag":http://iphoneincubator.com/blog/debugging/how-to-create-conditional-log-statements-in-xcode
