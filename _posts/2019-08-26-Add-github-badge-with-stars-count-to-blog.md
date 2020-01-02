---
layout: post
title:  "How to add github badge with stars count to my blog"
date:   2019-08-26 19:23:11
categories: github
---

Visit https://buttons.github.io/ first.

For my repo as an example: https://github.com/gazeldx/db_admin

* Step 1: 
```html
<!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/gazeldx/db_admin" data-icon="octicon-star" data-show-count="true" aria-label="Star gazeldx/db_admin on GitHub">Star</a>
```

* Step 2:
```html
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
```

FYI, I think buttons.js will call `https://api.github.com/repos/gazeldx/db_admin`.
 
The stars count is in the json of `stargazers_count`.