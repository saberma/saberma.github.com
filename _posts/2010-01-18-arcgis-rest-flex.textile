---                                                                                                                                                          
layout: post
title: ArcGIS(Flex+Rest)
category: other
---
h2. ArcGIS Server

h3. 概述

地理信息应用系统的后台服务器使用ArcGIS Server，前端使用Flex，之间运用Rest进行通信

h3. 安装

部署ArcGIS的服务器使用windows2003 server，打上SP2补丁
在ArcGIS Server 9.3版本的安装光盘中，选择安装ArcGIS Server for the Java Platform
安装后在开始菜单中的ArcGIS程序组运行Post install，程序会创建几个用户、用户组，并增加相应的SOM、SOC等服务

注意：
不用安装.Net Platform
不要使用administrator替换默认的用户(否则可能会出现权限问题)

h3. 配置

在程序组中打开ArcGIS Server Manager，增加一个服务add new service

h3. 检查

打开浏览器，输入地址:http://localhost:8399/arcgis/rest/
能正常访问就算成功

h2. Flex

h3. 安装Flex SDK

<pre>
wget http://download.macromedia.com/pub/flex/sdk/flex_sdk_3.5.zip
unzip flex_sdk_3.5.zip -d /usr/local/flex
wget http://downloads2.esri.com/Support/downloads/ao_/arcgis_api_for_flex_1_3.zip
unzip arcgis_api_for_flex_1_3.zip /ArcGIS_Flex/libs/agslib-1.3-2009-10-31.swc -d /usr/local/flex/frameworks/libs/

#在~/.bashrc中加入
export FLEX_HOME=/usr/local/flex
export PATH=$PATH:$FLEX_HOME/bin

#检查安装是否正确
mxmlc --version
</pre>

h2. 注意事项

h3. 清除Rest缓存

如果使用http://arcgishost:8399/arcgis/rest没有看到最新的MapServer
则需要进入http://192.168.1.47:8399/arcgis/rest/admin,选择Clear Cache Options,单击Clear Cache Now

h3. Flash跨域访问

"问题描述":http://bbs.esrichina-bj.cn/ESRI/viewthread.php?tid=37243
crossdomain.xml要放在ArcGIS安装目录ArcGIS\java\web_output下

h3. Rest中访问tile报404

在Map Server的caching中选中create tiles on demand，并且不要选中Allow clients to cache tiles locally

h2. 参考资料

"ESRI Flex Sample":http://resources.esri.com/help/9.3/arcgisserver/apis/flex/samples/index.html
"ArcGIS RIA背景":http://bbs.esrichina-bj.cn/ESRI/thread-47413-1-1.html
"ArcGIS API For Flex基础开发":http://bbs.esrichina-bj.cn/ESRI/thread-47700-1-1.html
"ArcGIS API For Flex高级开发":http://bbs.esrichina-bj.cn/ESRI/thread-48031-1-1.html
"制作Geoproccessing示例":http://webhelp.esri.com/arcgisserver/9.3/java/index.htm#geoprocessing/guide_-1963186772.htm
