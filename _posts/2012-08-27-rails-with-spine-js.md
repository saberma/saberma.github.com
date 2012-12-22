---
layout: post
title: Rails 项目集成 Spine.js
category: rails
published: false
---

[ShopQi](https://www.shopqi.com) 的后台管理是使用 Backgone.js 进行开发的，Backbone 最初的版本在 controller 和 view 之间的关系设计不大好，基本上 controller 的作用被弱化了。
Spine.js 作为后来者，大量借鉴了 Backbone 和 Rails 的模式，基于 jQuery，并使用 coffee 进行开发，代码量减少很多，维护进来也更容易。

下面以 [ShopQi homepage](https://github.com/saberma/homepage) 为例，将注册商店的操作改为使用 Spine.js 来实现

表单内容由服务端生成，客户端将接管表单按钮点击事件，进行校验后提交

## 安装

将 spine-rails 加到 `Gemfile` 的 assets 分组，production 环境不需要加载

    group :assets do
      # ...
      gem 'spine-rails'
    end

安装 Gem

    $ bundle install

## 初始化

    $ rails generate spine:new
    $ ls app/assets/javascripts/app
    controllers  index.js.coffee  lib  models  views

这里的目录结构将用于存放 js 文件

## 创建商店客户端 Resource

### 创建实体

    $ rails g spine:model Shop name admin_email admin_password admin_confirm_password
    $ cat app/assets/javascripts/app/models/shop.js.coffee
    class App.Shop extends Spine.Model
      @configure 'Shop', 'name', 'admin_email', 'admin_password', 'admin_confirm_password'
      @extend Spine.Model.Ajax

### 创建控制器

    $ rails g spine:controller shops 
    $ cat app/assets/javascripts/app/controllers/shops.js.coffee
    class App.Shops extends Spine.Controller
      # elements:
      #   '.items': items
      # 
      # events:
      #   'click .item': 'itemClick'

      constructor: ->
        super
        # ...

### 加载控制器

打开 `app/assets/javascripts/app/index.js.coffee`，在 Spine.Route.setup() 前面加入：

  class App extends Spine.Controller
    constructor: ->
      super
    new App.Shops el: @$('form')
    Spine.Route.setup()

### 加载 App

打开 `app/views/registrations/new.html.haml`，在最后面加入：

:javascript
  new App({el: "#app"})

### 监听表单点击事件

修改 `app/assets/javascripts/app/controllers/shops.js.coffee` 为：

    class App.Shops extends Spine.Controller
      events:
        'click button': 'create'

      create: ->
        shop = App.Shop.fromForm(@el)

## 资源

[Spine.js](http://spinejs.com)
