---                                                                                                                                                          
layout: post
title: Rails入门
category: rails
---
h2. 覆盖ActiveRecord属性读写方法

比如需要将属性photo的值进行Base64解码后再写入数据库中，可重写方法

{% highlight ruby %}
def photo=(encoded_photo)
  write_attribute(:photo, Base64.decode64(encoded_photo))
end

def photo
  Base64.encode64(read_attribute(:photo))
end
{% endhighlight %}

h2. 扩展

在rails项目中有时需要对model类进行扩展
比如扩展方法view_id，以获取包含model id和name的字符串

可以在models目录下新增extensions目录，新增 @base.rb@

{% highlight ruby %}
module Extensions
  module Base
    extend ActiveSupport::Concern

    module InstanceMethods
      def view_id
        "#{self.class.name.downcase}_#{self.id}"
      end
    end
  end
end
{% endhighlight %}

查看 "concern源码":https://github.com/rails/rails/blob/master/activesupport/lib/active_support/concern.rb 其中的append_features方法在module被include的时候会被调用
因此扩展类中引入ActiveSupport::Concern类后，会自动include InstanceMethods，如有ClassMethods方法，也会自动extend

在需要扩展的model类(比如Foo类)中引入扩展类

{% highlight ruby %}
class Foo
  include Extensions::Base
end
{% endhighlight %}

参考:
"深入Rails3: ActiveSupport::Concern":http://ihower.tw/blog/archives/3949
