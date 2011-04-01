---                                                                                                                                                          
layout: post
title: 应用screen实现远程结对编程
category: linux
published: false
---

#make screen run with root
sudo chmod u+s /usr/bin/screen

h3. 修改绑定

绑定ctrl+a用于切换各个tab，会导致zsh的命令行无法跳转至行首

h3. 底端提示信息

把/etc/screenrc中的所有配置注释掉，避免title不及时更新的问题

h3. 指定base目录

http://haruska.com/2009/09/29/remote-pair-programming/
http://linuxtoy.org/archives/screen.html
http://www.youtube.com/watch?v=IFClpADY7Tc
