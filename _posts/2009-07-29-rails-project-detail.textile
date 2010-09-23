---                                                                                                                                                          
layout: post
title: 项目说明
category: rails
---
h2. 工作流

<pre>
#经考查,工作流引擎ruote是异步机制,无法保证业务实体与待办事项的处于同一事务
#因此,改用自己定义的流程程序
#相关流程代码放置在event.rb中
</pre>

h2. 枚举值

<pre>
#枚举值统一放置在config/initializes/enum.rb中
script/console
#获取"来电目的",返回值可作为option_for_select方法的参数
>> Enum.aim
=> [["求助", "1"], ["投诉", "2"], ["咨询", "3"], ["建议", "4"], ["举报", "5"], ["其他", "9"]]
#获取value=1的来电目的
>> Enum.aim 1
=> "求助"
</pre>
<pre>
#页面调用1
select:
f.select :agree Enum.sf
radio:
f.radio_enum :xingbie, 'sex'
#页面调用2(字段名与枚举类型名相同)
select:
f.select :aim
radio:
f.radio_enum :sex

#查看页面调用
@call.agree_enum_sf
#查看页面调用(字段名与枚举类型名相同)
@call.aim_enum
</pre>

h2. 多行文本内容查看

<pre>
#页面显示时应调用，以支持换行的显示(处理过程、往来记录的不用换行)
=simple_format @conversation.content
</pre>

h2. 日期格式

<pre>
#统一放置在config/initializes/datetime_format.rb中
ActiveSupport::CoreExtensions::Time::Conversions::DATE_FORMATS.merge!(
  :without_year => "%m-%d %H:%M:%S"
)
#创建时间格式化为"03-23 10:10:10"
created_at.to_s(:without_year)
#注意如果是日期类型的，应将格式放在
ActiveSupport::CoreExtensions::Date::Conversions::DATE_FORMATS.merge!(
  ::short => "%Y-%m-%d"
)
</pre>

<pre>
#注意：
#日期在页面显示时，要调用d方法，传入日期
#避免连接Oracle时，显示为英文格式
d conversation.finished_date
</pre>

h2. Rails核心方法重载

统一放在config/initializers目录下

<pre>
#对于视图层方法的重构放在view_helper.rb
</pre>

h2. 防止Form重复提交

项目中form提交方式分两种

# 普通提交，提交按钮调用link_to_submit方法，可参考users/new.haml
# ajax后台提交，提交按钮调用link_to_remote_submit方法，可参考roles/new.haml

两个方法都支持参数label，用于指定按钮显示名称，默认为"保存"
如link_to_submit(:commit)，则显示"提交"
