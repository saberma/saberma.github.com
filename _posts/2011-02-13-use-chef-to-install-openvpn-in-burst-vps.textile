---                                                                                                                                                          
layout: post
title: 应用chef安装openvpn
category: linux
#published: false
---
h2. burst vps

之前买了burst的vps，现在准备为它安装openvpn
使用openvpn需要先登录后台管理 "https://184.82.225.10:2408/index.php":https://184.82.225.10:2408
在Networking & Utilities 启用 Enable Tun/Tap

h2. 安装openvpn

假设vps ip为188.188.188.188
ssh密码为123456

把这台vps当成vpn服务器，实现无障碍访问互联网

pre. 
export EDITOR=vim
knife role create vpn

knife会打开vim，将内容修改为:

pre. 
{
  "name": "vpn",
  "description": "openvpn server",
  "json_class": "Chef::Role",
  "default_attributes": {
    "chef": {
      "server_url": "https://api.opscode.com/organizations/#{YOUR COMPANY!}",
      "cache_path": "/var/chef/cache",
      "backup_path": "/var/chef/backup",
      "validation_client_name": "#{YOUR COMPANY!}-validator",
      "run_path": "/var/chef"
    }
  },
  "override_attributes": {
  },
  "chef_type": "role",
  "run_list": [
    "recipe[openvpn::default]"
  ]
}

修改完后退出vim，会提示已经

bq. 
Created (or updated) role[vpn]

下载相应的cookbook

pre. 
#此命令最常用
knife cookbook site vendor openvpn

远程生成服务端的keys，以下命令在本地执行
在bash，dot点号命令与source命令一样，把文件加载至shell内存空间

修改/cookbooks/openvpn/templates/default/server.up.sh.erb，加入以下内容

bq. 
/sbin/sysctl -w net.ipv4.ip_forward=1
/sbin/iptables -t nat -A POSTROUTING -s <%= @openvpn[:subnet] %>/<%= @openvpn[:netmask] %> -j SNAT  --to-source <%= @openvpn[:local] %>

修改cookbooks/openvpn/recipes/default.rb，加入变量

bq. 
template "/etc/openvpn/server.up.sh" do
　source "server.up.sh.erb"
　variables :openvpn => node[:openvpn]
end

pre. 
cd files/default/easy-rsa
. vars
rake server

为node加入role

pre. 
knife node list
#以上查看node id，假如为201166，现在查看node的run_list
#加入刚才新增的role
knife node run_list add 201166 "role[vpn]"
knife cookbook upload -a

远程登录vps，开始按run_list运行指定recipe

pre. 
ssh root@188.188.188.188
chef-client

在本地配置vpn client

pre. 
sudo apt-get install openvpn
cd files/default/easy-rsa
. vars
#注意不要运行多次，多次运行的时候请更换name，否则生成的.crt文件会为空
rake client name="client" gateway="188.188.188.188"
sudo mkdir /etc/openvpn
cd /etc/openvpn
sudo cp /tmp/client.zip .
sudo unzip client.zip

修改DNS，否则就算边上vpn也不能访问twitter等，修改 @/etc/resolv.conf@为:

bq. 
nameserver 8.8.8.8
nameserver 8.8.4.4

测试

pre. 
#在服务端运行
openvpn --config /etc/openvpn/server.conf --script-security 2
#在客户端运行
sudo openvpn --config /etc/openvpn/client.conf

此时，再通过浏览器访问的google.com不会再跳转至google.com.hk了

h2. ssh通道?

除了vpn，我们还可以使用ssh通道访问受限网站

pre. 
#autossh支持断线重连
sudo apt-get install autossh
autossh -M 2000 -N -v root@188.188.188.188 -D 127.0.0.1:7070

结合"chrome proxy switch":https://chrome.google.com/extensions/detail/caehdcpeofiiigpdhbabniblemipncjj?hl=zh-cn 可以让浏览器自动切换代理

h2. 调试

h3. 日志

pre. 
tail -f /var/log/openvpn.log

h2. 参考资源
"BurstNET的VPS安装OpenVPN教程总结":http://www.lenghost.cn/vps/burstnet-vps-openvpn-install/
"Openvpn Document":http://openvpn.net/index.php/open-source/documentation/howto.html
"ssh tunneling":http://wiki.wowubuntu.com/blog/ubuntu_ssh_tunneling
"Chrome自动使用代理":http://imyue.net/?p=1167
