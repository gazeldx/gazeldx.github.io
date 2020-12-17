---
layout: post
title:  "Coding Studying"
date:   2020-03-31 21:11:11
categories: Code
---

# Dashboard
## Logs
Logs are located at `./dashboard/log`.

For `logger.info` is logged into `production.log`, for `puts` is logged into `puma_stdout.log`.

# Apps
## Studio
### Make you change show up in development mode.
Read `apps/README.md`.

`npm run build` means build development App.

The js file like `http://localhost.code.org:3000/assets/js/code-studio.js` is actually pointed to 
`./dashboard/public/blockly/js/code-studio.js` which is actually a link to 
`/Users/lanezhang/projects/ruby/code-dot-org-staging/apps/build/package/js/code-studio.js`. 
They are not in `public/assets/js/code-studio.js`!

Files like `apps/src/sites/studio/pages/courses/index.js` will compiled under `apps/build/package/`.

If you find `http://localhost.code.org:3000/assets/js/courses/index.js` 404 error,
change `optimize_webpack_assets` to false in `locals.yml`.

With the power of `yarn start`, now change `apps/src/sites/studio/pages/courses/index.js` will immediately refreshed in your web!
You can enjoy your debugging now!  

## Structure
* Course list: `apps/src/sites/studio/pages/courses/index.js`

