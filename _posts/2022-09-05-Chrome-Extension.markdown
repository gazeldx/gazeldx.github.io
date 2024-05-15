---
layout: post
title:  "Chrome extension advanced research"
date:   2022-09-05 19:45:41
categories: Chrome-extension
---

# How to save a file into Downloads in a Chrome extension?
* Firstly, add these to `manifest.json`:

```json
{
    "manifest_version": 2,
    "name": "Chrome extension example 1",
    "permissions": [
        "downloads"
    ],
    "content_scripts": [
        {
            "matches": ["*://www.google.com/"],
            "run_at": "document_idle"
        }
    ],
    "background": {
        "scripts": ["background.js"],
        "persistent": false
    },
}
```

* Secondly, add these to `background.js`:

```javascript
/* jshint esversion: 6 */
/* jshint node: true */
'use strict';

console.log("===== Enter background.js ======");
const url = 'data:text/html;charset=utf-8,' + '<html>test</html>';
chrome.downloads.download({
  url: url,
  filename: '0_test.html'
});
console.log("===== 0_test.html saved to `Downloads` folder. ======");
```

* Thirdly, verify the result. Whenever you visit `www.google.com`, you should see `0_test.html` is saved into `Downloads` folder. 
