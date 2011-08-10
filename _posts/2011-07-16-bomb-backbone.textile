---                                                                                                                                                          
layout: post
title: BOMB! BACKBONE
category: jquery
published: false
---
这段时间一直使用backbone进行开发，越用越喜欢它，因为它真是一个难得的框架。如果一定要用一句话来评价它的话，那就是：That's What I Want.这个评价是我初见jQuery的那种感觉。现在基于我对它的了解，是时候向大家推荐它了:
*BOMB! BACKBONE!*


h2. Backbone

这是一个开源的轻量级javascript mvc框架， 轻量意味着它不提供任何UI Widget。
既然是我们熟悉的mvc框架，其整体架构就非常清晰，了解一下就很容易上手。

h3. 为什么需要它

自Ajax火爆流行之后，单页Web程序越来越受欢迎。但如果将整个网站都开发成无刷新的方式(twitter?)，复杂度还是不小，开发出来后我们还要考虑其可维护性。
而Backbone的出现，大大降低了开发此类网站的难度，维护性也很好。

h3. 稳定性

Backbone的发展非常的快，从2010-10-13发布0.1.0版本，到2010-12-01发布0.3.3版本(长期稳定版)，只用了一个半月就把一个优秀框架做出来了，团队效率非常高。
我们使用的版本就是0.3.3，最新版本是上周才发布的0.5.1，不要求新，0.3.3足够使用。

它的贡献者非常活跃，在github上拥有2843个follower，310个fork!
表明backbone在国外已经非常流行和稳定了，可以放心使用，不会第一个当小白鼠。

h2. 使用

h3. 基本用法

h3. 结合rails

rails使用to_json返回model，会把model name作为key，如:

pre. 
{user: {name: 'saberma'}}

而backbone的model只接受 name: 'saberma'

而不需要使用以下rails全局关闭root

pre. 
ActiveRecord::Base.include_root_in_json = false

h2. 我的实践总结

h3. 挑个模板引擎

我们最初使用的jQuery template，但不大好用，特别是在迭代处理上
后来换成了handlerbar.js，可以很方便地增加helper，不足之处就是无法使用 @if a==b@ 这样的语法，只能 @if flag@

h3. 多view

h3. 点击链接

点击链接触发事件的函数最后要返回false，否则会打开链接地址

h3. form

form最好都加上 @onsubmit='return false'@ 属性，避免js未加载完毕点击时直接提交表单
另一个好处就是 本来要用ajax提交的，結果函数发生错误导致form以传统方式提交，页面刷新了，就看不到函数错误提示

h2. 疑问

h3. rjs

rails新版的rjs已经挺好用了，后台可以直接返回js代码给浏览器执行，为什么还要用rjs

这个问题不好回答，我的感觉是backbone的mvc结构可以使js逻辑更加集中，管理方便

h2. 不足之处


h2. 其他

h3. CoffeeScript!

"coffeescript.org":coffeescript.org
如果你决定使用backbone开发，就意味着你一定会重度依赖js，而由于js的语法已经严重跟不上时代了，写起来代码繁多，不好维护，所以CoffeeScript一定会成为必备工具之一了。

h3. 类似项目


h3. 国内使用backbone的产品

据说豆辨说是使用backbone开发的，刚去看了js源码，确实如此，使用的版本也是0.3.3
希望国内的开发者可以更好地利用好backbone这个工具，开发出更具交互性的Web产品


h2. 参考资源

"node.js module":https://github.com/joyent/node/wiki/modules
"backbone官网":http://documentcloud.github.com/backbone/
"underscore官网":http://documentcloud.github.com/underscore/
