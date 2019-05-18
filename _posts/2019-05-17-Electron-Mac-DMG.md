---
layout: post
title: DMG builder using Electron
date:   2016-04-20 13:50:39
categories: javascript electron node.js react dmg bundler
author: Xun Li
---

## Toolchain

**Compiler**  Babel -- The compiler for next generation JavaScript

It is a good practice to write your React code in JSX,  so we first need to add Babel so we can compile JSX down into standard Javascript.
```
npm i babel-core --save-dev 
npm i babel-preset-es2015 babel-preset-react --save-dev
```

**
```
npm i gulp gulp-babel --save-dev # basic gulp
npm i gulp-concat gulp-css gulp-sourcemaps --save-dev # plugins
```

## React
