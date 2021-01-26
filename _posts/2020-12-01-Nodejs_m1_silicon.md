---
layout: post
title: Build Nodejs on Apple M1 Silicon
date:   2020-12-01 13:50:39
categories: Nodejs Apple M1 Silicon
author: Xun Li
---

Node.js can be installed and run on Apple M1 with Rosseta2. 

To get it running natively on M1, here are some notes to compile it from source: 


1. Build Nodejs with native Python 3.8 on Apple M1

Get 15.6.0 from github (Date: 2021-01-14): 

```Bach
curl -L -O https://github.com/nodejs/node/archive/v15.6.0.tar.gz
tar -xvf v15.6.0
cd node-15.6.0
```

Configure the node to build with arch arm64 on Apple's M1:
```Bash
./configure --dest-cpu=arm64
```

There is a bug in chrome V8 that is not yet included in latest Chrome V8 release (see https://github.com/nodejs/node/issues/36656), so you need to manually patch the fix here: https://chromium-review.googlesource.com/c/v8/v8/+/2609413/1/src/base/platform/platform-macos.cc

The file is under directory node-15.6.0/deps/v8/src/base/platform/platform-macos.cc

Save the edits. Then you can build and install it:

```Bash
make -j 4
sudo make install
```

2. Update file permissions

You will need to update the permission of ~/.node_repl_history, if you do sudo install. Just add write permission so there is no warning when you run node.

