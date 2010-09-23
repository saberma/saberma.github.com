---
layout: post
title: jQuery常用插件
category: jquery
---

jQuery的简洁高效，越来越受到程序员的欢迎了
这里介绍我使用过的jQuery插件

h2. jrails

jrails不算是一个jquery的插件，而是一个js适配器
用于将rails自带的js框架prototype，无缝地替换为jQuery
强烈推荐使用
"project":http://ennerchi.com/projects/jrails

h2. facebox

实现lightbox功能
"project":http://plugins.jquery.com/project/facebox
手动处理：修正跟blueprintcss框架冲突导致facebox边框显示重叠的问题
"demo":http://famspam.com/facebox/

h2. times

定时器
"project":http://plugins.jquery.com/project/timers
"demo":http://jquery.offput.ca/timers/

h2. tooltip

title属性指定指示信息显得有点简陋了，用tooltip实现Div层提示
手动处理：修正在firefox下提示层定位不准的问题
"project":http://flowplayer.org/tools/index.html
"demo":http://flowplayer.org/tools/tooltip.html

h2. easy slider

内容较大(多行)的情况下，实现滚动显示内容
手动处理：增加了鼠标移入时暂停滚动，修正滚动至最后一项后不能平滑过渡到第一项的问题
"project":http://cssglobe.com/post/5780/easy-slider-17-numeric-navigation-jquery-slider
"demo":http://cssglobe.com/lab/easyslider1.7/03.html

h2. Jcrop

用于框选图片中的区域，以实现定位或对图片进行裁剪
"project":http://plugins.jquery.com/project/Jcrop
"demo":http://deepliquid.com/projects/Jcrop/demos.php

h2. label over

一般情况下，输入框旁边都会带上label说明
在布局较小的情况下，也许你希望输入框内容直接显示说明
当用户输入内容时，说明内容自动清除，label over就是实现此功能的插件
"project":http://plugins.jquery.com/project/labelover
"demo":http://remysharp.com/wp-content/uploads/2007/03/label_over_example.html

h2. ZeroClipboard

结合Flash实现跨浏览器复制网页中内容(或当前地址)到剪贴板
严格来说这个不算jQuery的插件:)
"project":http://code.google.com/p/zeroclipboard/

h2. MultiFile

实现浏览器多文件上传时，只显示一个文件浏览按钮

<pre>
#修正删除按钮样式
$.fn.MultiFile.options.STRING.remove = '<a class="icon-16px-box j-icon i-close">&nbsp;</a>';
</pre>

"project":http://www.fyneworks.com/jquery/multiple-file-upload/

h2. JQZoom

放大查看图片某一部分，网购产品必备

"project":http://www.mind-projects.it/projects/jqzoom
"demo":http://www.mind-projects.it/projects/jqzoom/demos.php

h2. jCarousellite

显示图片组

"project":http://www.gmarwaha.com/jquery/jcarousellite/index.php
