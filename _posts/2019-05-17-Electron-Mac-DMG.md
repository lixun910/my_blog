---
layout: post
title: DMG builder using Electron
date:   2016-04-20 13:50:39
categories: javascript electron node.js react dmg bundler
author: Xun Li
---

## Use Bootstrap in Electron

Bootstrap depends on two modules: `jquery` and `proper.js`

```Bash
npm install popper.js --save
npm install jquery --save
npm install bootstrap --save
```

To use `Bootstrap` (both style and scripts) in Electron, one needs to require 
the jquery.js and bootstrap.js

```javascript
const $ = require('jquery');
require('popper');
require('bootstrap')
```

To include the css files:
```html
  <link href="../node_modules/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" crossorigin="anonymous">
```

## Toolchain

**Compiler**  Babel -- The compiler for next generation JavaScript

It is a good practice to write your React code in JSX,  so we first need to add Babel so we can compile JSX down into standard Javascript.
```
npm install --save-dev @babel/core @babel/cli @babel/preset-env
npm install --save @babel/polyfill
```

**
```
npm rm --global gulp
npm install --global gulp-cli
npm install --save-dev gulp
npm install --save-dev gulp-css
```

## React


## Electron Packaging

```
npm i electron-builder --save-dev
```

point the tool to the folder with the code to be compiled through the package.json by adding:

```
"build": {
  "appId": "com.test.electrate",
  "files": [
    "app/**/*",
    "node_modules/**/*",
    "package.json"
  ],
  "publish": null
}
```

In gulpfile.js
```
gulp.task('release', ['copy', 'build'], () => {
    spawn(
        'node_modules/.bin/electron-builder', 
        ['.'], 
        { stdio: 'inherit' }
    ).on('close', () => process.exit());
});
```