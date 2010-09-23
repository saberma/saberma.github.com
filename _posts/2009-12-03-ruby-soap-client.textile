---                                                                                                                                                          
layout: post
title: soap客户端
category: rails
---
h2. 选择

ruby带有soap4r的插件，可以用于访问web service服务接口，但不支持basic_auth、WS-Security
因此选用handsoap作为客户端插件

h2. 安装

<pre>
#curb在window下安装很麻烦，改为指定httpclient
#sudo gem install handsoap curb nokogiri httpclient --no-ri --no-rdoc
sudo gem install handsoap nokogiri httpclient --no-ri --no-rdoc
#如果安装curb过程报cannot find -lcur错误，则运行
sudo apt-get install libcurl4-openssl-dev
</pre>

<pre>
#environment.rb中加入
config.gem 'handsoap', :lib => 'handsoap', :source => "http://gems.github.com"

#curb在window下安装很麻烦，改为指定httpclient，environment.rb中最底部加入
Handsoap.http_driver=:httpclient
</pre>

h2. 使用

<pre>
#生成客户端类
script/generate handsoap http://192.168.1.20:8081/cmis/webService/ciisStatService?wsdl
</pre>

配置管理员需要配置的地方在这里(用户、密码、接口地址)

<pre>
#生成的连接地址加入environments/production.rb中
CIIS_USERNAME = 'cogent'
CIIS_PASSWORD = 'cogent'

CIIS_STAT_SERVICE_IMPL_SERVICE_ENDPOINT = {
  :uri => 'http://192.168.1.20:8081/cmis/webService/ciisStatService',
  :version => 1
}
</pre>

<pre>
#script/console
s = Ciis::PopService.find_pop '%'
#获取列表对象
s.list
#获取列表对象中的内容
s.list.first.name
#获取属性值
s.size
</pre>

h2. 调试

使用soapui访问webservice接口，确保服务正常
为了监控客户端与服务器的交互内容，可使用tcpmon架设代理

"soapui":http://sourceforge.net/projects/soapui/files/soapui/3.0.1
"tcpmon":https://tcpmon.dev.java.net

"soapui简要介绍":http://boyun.sh.cn/blog/?p=1076

注意，连接接口时要设置用户名密码，双击某个接口方法的请求(一般为request1)，点击弹出的右边窗口左下方的[Aut]，输入用户名、密码
然后在Request1窗口中点击右键，选择[Add WSS Username Token]，会看到请求的xml中增加了用户名、密码

h2. 问题

<pre>
#按照handsoap官方说明无法实现ws-security的访问
#提示Security processing failed (actions mismatch)
#经过尝试，以下调用是成功的
  def on_create_document(doc)
    #register namespaces for the request
    doc.alias 'tns', 'http://ws.stat.wsif.cogent.com/'
    doc.alias 's', 'http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd'
    header = doc.find("Header")
    header.add "s:Security" do |s| 
      #增加此节点，否则会报错
      s.add "s:UsernameToken" do |t|                                                                                                                         
        t.add "s:Username", "cogent"
        t.add "s:Password", "cogent"
      end 
    end 
  end
</pre>

［第二个问题］开发环境下报错：ArgumentError: A copy of Ciis::PopService has been removed from the module tree but is still active!

<pre>
#重现步骤
script/console
  Ciis::PopService.find_pop('%') 
  reload!
  #再次执行报错
  Ciis::PopService.find_pop('%')
</pre>

产品环境下没问题，但由于开发环境下class会被缓存，而handsoap静态方法引用了实例对象
在handsoap源代码中发现以下语句

<pre>
@@instance = {}
def self.instance
  @@instance[self.to_s] ||= self.new
end 
def self.method_missing(method, *args, &block)
  if instance.respond_to?(method)
    instance.__send__ method, *args, &block
  else
    super
  end 
end
</pre>

因此在开发环境下需要重载instance静态方法

<pre>
#config/initializers/fixed_handsoap_reload_error.rb
unless development?
  module Handsoap
    class Service
      def self.instance
        self.new
      end 
    end 
  end 
end
</pre>

<pre>
#测试是否解决问题了
script/console
  #确保每次获取的实例对象不一样
  Ciis::PopService.instance
  Ciis::PopService.instance
</pre>
