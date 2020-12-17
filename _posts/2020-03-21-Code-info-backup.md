---
layout: post
title:  "Coding info backup"
date:   2020-03-31 21:11:11
categories: code
---

* Change config.yml.erb db_writer: need_to_set_as_sequel describe.
~/p/r/c/dashboard (master) [1]> RAILS_ENV=production bundle exec rails assets:precompile
rails aborted!
NoMethodError: undefined method `to_sym' for nil:NilClass
/Users/lanezhang/projects/ruby/code-dot-org-staging/lib/cdo/db.rb:76:in `sequel_connect'
