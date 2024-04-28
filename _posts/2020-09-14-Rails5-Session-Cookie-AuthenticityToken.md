---
layout: post
title:  "Rails 4 5.0 Session Cookie AuthenticityToken"
date:   2020-09-14 21:11:11
categories: Rails
---

# session_store

`config/initializers/session_store.rb`

```ruby
Rails.application.config.session_store :cookie_store, key: '_your_session', secure: false, domain: :all
secure is set to true when https enabled. 
```

# LoginController

```ruby
class LoginController < ActionController::Base
  # https://stackoverflow.com/questions/38331496/rails-5-actioncontrollerinvalidauthenticitytoken-error
  protect_from_forgery prepend: true

  def show
    @student = Student.new
  end

  def login
    @student = Student.find_by_username(params.require(:student)[:username])

    if @student.nil?
      flash[:error] = '用户不存在！'
      redirect_to login_path
    else
      if @student.password == params.require(:student)[:password]
        login_success_process
      else
        flash[:error] = '密码错误！'
        flash[:username] = params.require(:student)[:username]

        redirect_to login_path
      end
    end
  end

  def logout
    clear_session

    flash[:notice] = 'Logout successfully!'
    redirect_to login_path
  end

  private

  def login_success_process
    set_login_session

    flash[:notice] = 'Login successfully!'
    redirect_to root_path
  end

  def set_login_session
    session[:id] = @student.id
    session[:username] = @student.username
    session[:name] = @student.name
  end

  def clear_session
    [:id, :username, :name].each do |key|
      session[key] = nil
    end
  end
end
```

# login.html.haml
```haml
!!! 5
%html{dir: locale_dir}
  %head
    %title= '请登录'
    = csrf_meta_tags

  %body
    - if session[:username]
      = session[:username]
      = '您已经登录！'
    - else
      %h1= t('signin_form.title')

      .flex-container
        #signin
          = form_for @student, url: do_login_path do |f|
            = error_msg @student

            .form-group
              = content_tag(:label, '用户名')
              .col-sm-9
                = f.text_field :username, required: true

            .form-group
              = content_tag(:label, '密码')
              .col-sm-9
                = f.password_field :password, required: true

            %br
            = content_tag(:button, raw("登录"), { type: 'submit' })
```
