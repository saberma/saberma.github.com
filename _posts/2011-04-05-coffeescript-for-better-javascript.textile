---                                                                                                                                                          
layout: post
title: coffeescript编写良好的javascript代码
category: jquery
published: false
---
为了在浏览器提升操作的流畅度，我们编写的javascript代码越来越多了
而javascript的语法太不简洁了，代码量太多意味着维护的成本也高了

最近，开源社区出来了一款小型编译器，用简洁的语法来编写代码，再转换成原生的js代码
这款编译器的名称叫做coffee-script

h2. CoffeeScript

官方地址: "http://jashkenas.github.com/coffee-script/":http://jashkenas.github.com/coffee-script

!/images/article/coffeescript/coffeescript.png!
  
上图左边是coffeescript代码，右边是最终生成的js代码
可以看到，coffeescript的语法与原生的js语法非常相近，但更为简洁

h3. 怎么与rails结合

coffeescript代码编写后，是需要手动运行命令进行编译的
在rails有相应的插件barista，可以做到即时编译，让编译过程透明化

barista的官网地址 "https://github.com/Sutto/barista":https://github.com/Sutto/barista
