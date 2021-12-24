---
layout: post
title:  "How to use Hotwire turbo in Rails 6 with Webpacker?"
date:   2021-11-11 10:16:41
categories: Rails Webpacker Turbo Hotwire Bootstrap5
---
# Background

[Turbo](https://turbo.hotwired.dev/handbook/introduction) can help refresh only a part of our webpage and don't need to refresh the whole page.

If we build a Single Page App or traditional web, we can do this by Ajax with json and JavaScript.

Now we have `Turbo` and don't need to write Ajax, json or JavaScript to make it.

`Turbolinks` is the old name of Turbo.

From https://github.com/hotwired/turbo-rails , we can see the [official example video](https://hotwired.dev/#screencast) is represented with assets pipeline, not with Webpacker.

# Goal
Here I will let you know how to integrate `Turbo` with Webpacker and Bootstrap 5.

Imagine that we have a navigation bar with some links. Each of those links maps to a Rails route.
We hope that when we click those links, the navigation itself, the header and the footer not to be refreshed.

# Steps
* Create a Rails 6 project (the default is using webpacker).
* Add `gem 'turbo-rails'` to and remove `gem 'turbolinks'` from `Gemfile`.
* Run `bundle install`.
* Run `rails turbo:install`.
* Run `yarn add @hotwired/turbo-rails`.
* Find a file like `frontend.js` under `app/javascript/packs/` and add `import "@hotwired/turbo-rails"` to the first line.
Then remove `import Turbolinks from "turbolinks"` and `Turbolinks.start()` from this file.

Also in this file, add these code to make the frame have a blue border:
```css
turbo-frame {
  display: block;
  border: 1px solid blue;
}
```

* In the file `app/views/layouts/application.html.erb`, add these:

```html
<head>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= javascript_pack_tag 'frontend' %>
    <%= stylesheet_pack_tag 'frontend' %>
</head>

<div class="row">
  <ul>
    <li>
      <a data-turbo-frame="main_frame" href="<%= products_path %>">Product List </a>
    </li>
    <li>
      <a data-turbo-frame="main_frame" href="<%= cdrs_path %>">CDR List </a>
    </li>
  </ul>
</div>    

<div class="row">
  <turbo-frame id="main_frame">
    <%= yield %>
  </turbo-frame>
</div>
```

* In `app/controllers/products_controller.rb`, write this:

```ruby
class ProductsController < BaseController
  # Remember to comment out this line like this. Just don't use a layout specifically. If you write a layout specifically, the layout will always be used!
  # Also don't use `layout false`, otherwise when user open the url in a new browser tab, the default layout `application` was lost.
  # In the end, you will have only one layout for the `Turbo` usage, that is the `app/views/layouts/application.html.erb`. And you need never specifically write `layout 'application'`. 
  # That is OK and good enough because you can dynamically generate the content of this `application.html.erb`.
  # You can have other logouts for other pages which don't use `Turbo` like `sign_in` or `register` pages.
  # layout 'application'

  def index
    @products = Product.all
  end
end
```

* In `app/views/products/index.html.erb`, write this:

```html
<%= turbo_frame_tag "main_frame" do %>
  <div class="row">
    <h4>Product List</h4>
  </div>

  <div class="row">
    <div>
      <div class="card">
        <div class="card-body">
          <div class="table-responsive">
            <table class="table table-centered table-borderless table-hover nowrap">
              <thead class="table-light">
                <tr>
                  <th>Product Name</th>
                  <th>Actions</th>
                </tr>
              </thead>

              <tbody>
                <% @products.each do |product| %>
                  <tr>
                    <td><%= product.name %></td>
                    <td>
                      <%= button_to i18n_action(:delete), product_path(product), method: :delete, data: { confirm: i18n_action(:delete_confirm), disable_with: 'Deleting...' }, class: 'btn btn-primary btn-sm' %>
                    </td>
                  </tr>
                <% end %>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>

  <%= enable_tooltip %>
<% end %>
```

Now you can see when you click the `Product List` link, only the `main_frame` is refreshed.
And the html returned from the Server side is generated only from the code in `app/views/products/index.html.erb`.
Code of `app/views/layouts/application.html.erb` are not used at all.

# Tips
* According to https://discuss.hotwired.dev/t/destroy-record-in-turbo-frame/2731/17 , you will have to use `button_to` tag like above in the `app/views/products/index.html.erb` to make the record deletion.

* The Bootstrap tooltip will not be displayed because the html in `main_frame` is generated after you click the nav link. 
So you will have to add this helper method:

```ruby
def enable_tooltip
  raw '<script>
var tooltipTriggerList = [].slice.call(document.querySelectorAll(\'[data-bs-toggle="tooltip"]\'))
var tooltipList = tooltipTriggerList.map(function (tooltipTriggerEl) {
  return new bootstrap.Tooltip(tooltipTriggerEl)
})
</script>'
end
```
and call this method in `app/views/products/index.html.erb` like the example above.

* If you are using `Devise`, you may find that the error message may not be displayed when typing a wrong password.

My solution is that you don't use `app/javascript/packs/frontend.js` in the layout of sign_in page.

Instead, you can write a new one like `app/javascript/packs/blank.js`, in this `blank.js`, don't add `import "@hotwired/turbo-rails"`.

And use the `blank.js` in the new layout file `app/views/layouts/blank.html.erb` for the Devise `sign_in` page
, like this:

```html
<head>
    <%= javascript_pack_tag 'blank' %>
    <%= stylesheet_pack_tag 'blank' %>
</head>
```
