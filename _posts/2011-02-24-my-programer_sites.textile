---                                                                                                                                                          
layout: post
title: 程序员必须知道的网站
category: other
published: false
---
我的程序出错了!调了很久都解决不了，同事也没有办法!
我已经尽力了，现在只能上网找人帮忙了!

问题只有在能够重现的情况下，才能快速被定位，继而被解决!
所以，在找他们帮忙前，我得先做点准备，以便让其他人快速重现问题。

根据问题的不同，以下这些网站可以让我们更好的实现远程协作。
友情提示：如果有人找你帮忙，你可以把这个文章发给他看看。

h2. js调试出错了

"http://jsbin.com":http://jsbin.com

!/images/article/sites/jsbin.png!

jsbin是在线js调试网站，左右两边分别是编写js代码和html代码的地方
右边红色区域的下拉框可以快速引入各种js框架(jQuery)

根据你的问题，写好js和html代码后，点击[preview]看看，能够重现问题了，就点击[save]按钮
左上角红色区域就会显示一段url地址，把它发到论坛或者你的朋友，他们就快速重现问题后就可以边修改边调试了

h2. 正则表达式結果不对

"http://www.rubular.com":http://www.rubular.com

!/images/article/sites/rubular.png!

rubular是提供给ruby程序员使用的在线正则表达试调试网站，修改表达式或者待匹配字符串都会即时更新結果集，操作体验非常好
输入表达式及待匹配字符串后，点击[make permalink]，上方就会显示url地址了，如上图红色区域所示，把url发出去吧

h2. 其他

"https://gist.github.com":https://gist.github.com

!/images/article/sites/gist.png!

gist是github提供的服务之一，用于代码片段存储，并支持各种编程语言的高亮，类似于"http://pastie.org":http://pastie.org (在国内无法使用，否则也是不错)

把可以重现问题的代码放到gist上，然后把当前url(如https://gist.github.com/515543)发出去
虽然无法在线调试，起码不需要别人在论坛上复制、粘贴代码保存至某个文件(多个文件?...)

使用gist请先了解git，最好用的版本控制软件

如果你有更好的网站方便程序员远程协作的，可以留言告知，thx
