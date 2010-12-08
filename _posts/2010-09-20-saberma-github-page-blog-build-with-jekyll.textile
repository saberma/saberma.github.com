---
layout: post
title: 应用jekyll构建基于github page的博客
category: other
---
基于github page的博客好处:

bq. 
简单至极，且完全免费
可以支持rss订阅，评论功能
依赖于git，文章原生支持版本控制(对比)，更有利于知识库类文章
可以使用vim编写文章，写的时候像在写代码，更符合程序员的习惯

本博客此次除了启用全新个人域名saberma.me，将做以下调整

bq. 
美化网站风格，更加的清新:)
页面布局调整为html5、css3架构
文章可按分类显示
代码增加高亮显示
增加rss feed输出

h2. 挑个新的博客模板

要求是基于html5、css3的免费模板，google之，发现个好地方: "http://freehtml5templates.com":http://freehtml5templates.com
一页一页的挑吧，总会找到自己喜欢的，可以根据右下角的Tag Cloud进行筛选，现在看到本站的新样子就是找好久才看中的模板

h2. 绑定独立域名

在godaddy中注册 "saberma.me":http://saberma.em 域名，.me专用于博客类型，但比.com贵一些，且没有优惠

# 注册完域名后，在域名管理中增加A record并指向207.97.227.245
# 在你的github项目下增加CNAME文件，内容为你的域名，如 "http://github.com/saberma/saberma.github.com/blob/master/CNAME":http://github.com/saberma/saberma.github.com/blob/master/CNAME

具体参考 "http://pages.github.com":http://pages.github.com 中 @Custom Domains@ 部分的内容

h2. pygments代码高亮

h3. 安装pygments

{% highlight bash %}
# On Ubuntu 安装
sudo apt-get install python-pygments
{% endhighlight %}

"完整安装说明":http://wiki.github.com/mojombo/jekyll/install

h3. 生成高亮显示的css文件

选择喜欢的样式，记下名称
"http://pygments.org/demo/6622":http://pygments.org/demo/6622

我选择的是fruity style，作为pygmentize命令style的参数值

{% highlight bash %}
# 生成相应的css
pygmentize -S fruity -f html > stylesheets/syntax.css
{% endhighlight %}

"参考pygments Command line usage":http://pygments.org/docs/quickstart/

h3. 如何使用

语法高亮的代码段

<pre>
{{ "{% highlight ruby" }} %}
def foo
  puts 'foo'
end
{{ "{% endhighlight" }} %}
</pre>

highlight后面第一个参数为language，如php，也可以是ruby控制台irb，更多lanuages可以查询 "http://pygments.org/docs/lexers/":http://pygments.org/docs/lexers/
第一个参数为必填，不填会导致_site目录生成不了相应的html文件，第二个参数显示行号

"参考jekyll说明":http://wiki.github.com/mojombo/jekyll/liquid-extensions

h2. Markdown标记与Liquid逻辑处理

h3. 两个知识点

* 为避免直接编写html代码，编写文章时，内容需要加入标记信息，即Markdown
* 博客中都是需要经过处理的，比如逻辑判断处理、循环处理，jekyll应用liquid模板语言进行这些处理

h3. Markdown

标记语言有很多种，如textile
这些标记语言会被标记引擎转换，输出成相应的目标格式（大部分情况是输出成html）
引擎也有很多种，不同的编程语言有不同的实现，ruby常用的引擎有RedCloth

h3. Liquid

简单来说，凡是看到{{ "{{" }}}}或者{{ "{%" }} %}包含的内容都是会被Liquid引擎处理的

比如*将日期格式化*的liquid语句

<pre>
{{ "{{ post.date | date_to_string" }} }}
</pre>

除了标记的Liquid语法外，jekyll还扩展出了几个便利的方法，其中有上面介绍的highlight方法
"jekyll liquid扩展":http://wiki.github.com/mojombo/jekyll/liquid-extensions
"liquid参考资料":http://github.com/tobi/liquid/wiki/Liquid-for-Designers

h2. 整合评论

由于github page最终生成的都是静态html页面，所以是没有评论功能呢
但我们利用disqus实现在线评论功能，先到 "http://disqus.com":http://disqus.com 注册帐号(免费)
注册成功后，为简单起见，只要把 @_includes/post.html@ 中的saberma替换为你的注册帐号就行了(disqus_url输入你实际的域名)

h2. 整合rss订阅

因为jekyll可以生成blogs列表，所以我们可以编写atom.xml，由jekyll生成最终xml结果
"这是我的atom.xml文件":/atom.xml

将生成的xml地址提交至 "feedsky.com":http://www.feedsky.com ，由feedsky进行管理和美化

"Setting Up an Atom Feed at GitHub Pages":http://elemel.se/2009/01/25/setting-up-an-atom-feed-at-github-pages.html

h2. 你也想弄一个github page博客?

最快的做法是 "fork我的博客":http://github.com/saberma/saberma.github.com ，git clone到你的电脑
然后修改成你的，具体需要调整的地方是:

# 删除_posts中的文章
# 按上面介绍的[整合评论]修改 @_includes/post.htm@ 文件
# 按上面介绍的[整合rss]修改 @atom.xml@ 文件
# 修改CNAME的内容为你的独立域名
# 运行jekyll，看看效果
# 上传!ok，访问你的blog地址看看

h2. 参考资源

"publishing-a-blog-with-github-pages-and-jekyll":http://blog.envylabs.com/2009/08/publishing-a-blog-with-github-pages-and-jekyll/
"jekyll wiki":http://github.com/mojombo/jekyll/wiki
"http://pages.github.com":http://pages.github.com 
