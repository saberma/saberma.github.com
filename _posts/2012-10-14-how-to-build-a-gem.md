---
layout: post
title: 创建一个 Ruby Gem
category: rails
published: true
---

假设我们要做了快递查询的 Gem

## 准备

RubyGems.org 有详细介绍 Gem 的开发过程[指南](http://guides.rubygems.org/make-your-own-gem/)，不过实际开发时我们会使用 [Jeweler](https://github.com/technicalpickles/jeweler) 来简化开发流程

    gem install jeweler --no-ri --no-rdoc

## 初始化

    $ jeweler china-express1 --summary 中国快递查询 --user-name saberma --user-email mahb45@gmail.com --yard --rspec
    $ cd china-express
    $ bundle

__bundle 前最好删除 Gemfile 中的 rdoc, rcov__

## 开发

在 rspec 目录中增加单元测试，lib 目录是写代码
详见 https://github.com/saberma/china-express

## 本地安装

    rake install

## 发布

    rake version:bump:patch
    # version:bump:minor
    rake release
