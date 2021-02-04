---
layout: post
title: Build arm64 libJpeg on Apple M1 Silicon
date:   2020-10-01 13:50:39
categories: libjpeg Apple M1 Silicon Chip
author: Xun Li
---

libJPEG is a must have dependency for many libraries, such as Pillow, GDAL etc. To get it compiled natively on Apple M1 chip, here are some notes:

# libJPEG source

You can get the latest libjpeg from it's offical site: http://ijg.org

Note: there are many other libjpeg "sites" or "pages" that could mislead you to an older version.

To date, the latest version is http://ijg.org/files/jpegsrc.v9d.tar.gz

Download and unzip it:

```shell
curl -L -O http://ijg.org/files/jpegsrc.v9d.tar.gz
tar -xvf jpegsrc.v9d.tar.gz
cd jpeg-9d
```

# Install XCode Command Line Tool

XCode Command Line Tools is free to install

```shell
sudo xcode-select --install
```

# Build libJPEG

```bash
./configure CFLAGS="-arch arm64" CXXFLAGS="-arch arm64" LDFLAGS="-arch arm64
make
sudo make install
```

Here you need `sudo` to install it to /usr/local, however, you can specify where to install libJPEG by using --prefix=/dir in the ./configure command.

# Check your libJPEG library

To check if your libJEPG is compiled with arm64 architecture

```shell
lipo /usr/local/lib/libjpeg.dylib
```

You will see something like this
```
Non-fat file: /usr/local/lib/libjpeg.dylib is architecture: arm64
```

Now, we are good to go!