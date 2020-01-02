---
layout: post
title:  "Rails6 with Devise, Webpacker and Bootstrap integration"
date:   2019-07-09 13:54:42
categories: Devise
---

# RVM or rbenv
Install one of this two soft.

# Rails
    gem install rails --pre

    rails new myapp --webpack -d postgresql
    
# Webpacker
Read and do what https://github.com/rails/webpacker said.

    rails webpacker:install
    
Should install `yarn` successfully. 

`webpacker.yml`需要配置正确。

首先，`webpack_compile_output: false`表示改动了`javascript/packs`下的文件，也不会生成新的文件，
development下默认为false正常情况下够用了。

另外，我发现如果`javascript/packs`下有同名但后缀不同的文件，如：`app.js`和`app.scss`，会有冲突（这估计是webpacker的一个bug），导致明明改动了application.js，却在界面上没有引用新的.js。
最好改成如：`app.js`和`app_css.scss`（不同名就可以了），因为这样的话，在`public/packs/js`中会产生 `application-a6101066db4a6301a095.js`和`application_css-eac9a902a3d86406ebbf.js`，这就完全不会有冲突发生了。
但这种方法解决不够完美。完美的做法是就保持同名，设置`extract_css: true`，并执行`$ ./bin/webpack-dev-server`把webpack-dev-server起起来。 

`extract_css`是含义是为true时，页面上会显式引入.css文件，development下设置为false就可以了，因为生成的.js中会用js的方式引入css。

# Devise
Read and do what https://github.com/plataformatec/devise/wiki said.

# Bootstrap
Read and do what https://gist.github.com/yalab/cad361056bae02a5f45d1ace7f1d86ef said.

    yarn add bootstrap@4.3.1 jquery popper.js

In `config/webpack/environment.js` add:
```javascript
environment.plugins.prepend('Provide', new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery',
  Popper: ['popper.js', 'default']
}))
```

# Integrate
`layout.html.erb`
`<%= stylesheet_pack_tag 'sign_in' %>`这样的写法需要在`app/javascript/packs/`下存在`sign_in.scss`.

