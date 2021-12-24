---
layout: post
title:  "Rails 6 Credentials (master.key and credentials.yml.enc)"
date:   2021-10-13 10:16:41
categories: Rails Credentials master.key
---

You can start with https://blog.saeloun.com/2019/10/10/rails-6-adds-support-for-multi-environment-credentials.html

and https://github.com/rails/rails/issues/30006

For `test` and `development` env, you can simply remove the `master.key` and you will find that `rails s` works well.
You can run `rails console`, then run `Rails.application.credentials.config` to see that the value.

But if you have a wrong `master.key` there and run `rails s`, you will get an error.

But if you removed `master.key`, you will find that `rails s -e production` doesn't work.

If you have the correct value of `master.key`, you can run `EDITOR=vim rails credentials:edit` to edit it.

If you don't have the correct value of `master.key`, when you run `EDITOR=vim rails credentials:edit`,
it will generate a new `master.key` for you but unfortunately that `master.key` is a wrong one.
This is reasonable because it makes the `credentials.yml.enc` unable to be decrypt unless you have already got a correct `master.key`.

So you can remove the `credentials.yml.enc` and `master.key` and run `EDITOR=vim rails credentials:edit` to generate a new pair.
But before you do that, you should remove `master.key` and run `rails console`, then run `Rails.application.credentials.config` to 
understand what values you need to set when running `EDITOR=vim rails credentials:edit`.

All the Rails instance in `production` env should have the same `credentials.yml.enc` and `master.key`.

So you should keep `credentials.yml.enc` in your sources code.
