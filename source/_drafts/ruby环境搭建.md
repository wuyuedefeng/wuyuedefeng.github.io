title: ruby环境搭建
author: wsen
date: 2017-03-04 10:58:02
tags:
---
### 安装配置

rvm ruby ..

[ruby-china wike](https://ruby-china.org/wiki)

#### [RVM安装ruby](https://ruby-china.org/wiki/rvm-guide)
查看安装的ruby
```
rvm list
```
安装指定版本ruby
```
rvm install ruby 2.2.2 
```
使用制定版本ruby
```
rvm use 2.2.2
```

#### gem源更换
查看所有`gem`源
```
$ gem sources -l
```

移除一个`gem`源
```
$ gem sources --remove https://rubygems.org/
```

添加一个`gem`源
```
$ gem source -a https://gems.ruby-china.org
```

#### 安装rails
gem安装`rails`
```
$ gem install rails -v 4.2.3
```

`rails`创建项目
```
$ rails new [myPro] [--skip-bundle]
```

#### [路由配置](http://guides.ruby-china.org/routing.html)
eg:
```
# 手动配置posts的show路由   as配置路由名称
  # get 'posts/:id', :to => 'posts#show', :as => 'show_post'

  #  排除show路由
  # resources :posts, :except => :show

  resources :posts do
    # posts/recent
    # get 'recent', :on => :collection
    #  集合路由
    collection do
      # posts/recent
      get 'recent'
    end
    #  成员路由
    member do
      # posts/:id/recent
      get 'recent'
    end
  end
```
