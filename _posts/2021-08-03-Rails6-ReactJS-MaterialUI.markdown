---
layout: post
title:  "Rails 6, ReactJS, Material UI"
date:   2021-08-03 21:11:11
categories: Rails6
---

# Installation
* Install [rbenv](https://github.com/rbenv/rbenv) with the latest ruby version
* Then,
```shell
gem install bundler
gem install rails
```

# Initialize a Rails app
```shell
rails new myapp -d postgresql --webpack=react
createuser -s myapp
rails db:create:all
rails server # Visit http://127.0.0.1:3000/
```


