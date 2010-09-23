---                                                                                                                                                          
layout: post
title: 敏捷开发Selenium
category: rails
---
Selenium作为验收测试工作，非常好用

h1. 启动selenium测试

<pre>
#启动测试环境
mongrel_rails start -e test -d
</pre>

"打开测试地址":http:/localhost:3000/selenium
点击右上角的执行按钮，开始测试

h1. 定位

假设有以下DOM元素

<pre>
<tbody>
  <tr>    
    <td>    
      <a href="/workitems/580407130/edit" class="i-submit" name="deal" title="处理"></a>
    </td>    
  </tr>    
  <tr>    
    <td>    
      <a href="/workitems/612486603/edit" class="i-submit" name="deal" title="处理"></a>
    </td>    
  </tr>
</tbody>
</pre>

要求：测试点击第二个“处理”按钮

<pre>
#先尝试使用css=a[@title='处理']:nth-child(2)或css=.i-submit:nth-child(2)方式定位
#结果是无法正常定位的，只能定位到第一个，估计是只有a元素是相邻关系时才能使用
#经过实践，最好的定位方式为：
click "name=deal index=2"
#即根据name查找，并过滤出第二个索引的元素
#简写，selenium会先查找id=deal，再查找name=deal
click "deal index=2"
</pre>

h1. 参考资料

"Selenium commands":http://seleniumhq.org/docs/04_selenese_commands.html
