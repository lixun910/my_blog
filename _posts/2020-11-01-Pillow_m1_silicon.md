---
layout: post
title: Build arm64 Pillow on Apple M1 Silicon
date:   2020-11-01 13:50:39
categories: Pillow Apple M1 Silicon Chip
author: Xun Li
---

Here are some notes to compile Pillow from source with libjpeg support on M1 using arm64 architecture:

1. compile libjpeg from source

```Bash
curl -L -O http://ijg.org/files/jpegsrc.v9d.tar.gz
tar -xvf jpegsrc.v9d.tar.gz
cd jpeg-9d
./configure CFLAGS="-arch arm64" CXXFLAGS="-arch arm64" LDFLAGS="-arch arm64
make
sudo make install
```

Note: here you need `sudo` to install it to `/usr/local`, however, you can specify where to install libJPEG by using --prefix=/dir in the ./configure command.

To check if your libJEPG is compiled with arm64 architecture

```bash
lipo -info /usr/local/lib/libjpeg.dylib
```

You will see something like this
```
Non-fat file: /usr/local/lib/libjpeg.dylib is architecture: arm64
```

2. Compile Pillow for native Python 3.8 on Apple M1

Get Pillow-8.1.0 from pypi: 

```Bach
curl -L -O https://files.pythonhosted.org/packages/73/59/3192bb3bc554ccbd678bdb32993928cb566dccf32f65dac65ac7e89eb311/Pillow-8.1.0.tar.gz
tar -xvf Pillow-8.1.0.tar.gz
cd Pillow-8.1.0
```

Add the following lines after line 493 in setup.py, which tells the compiler to use -arch arm64 on Apple's M1 Arm Chip:
```Python
import platform
if platform.machine() == 'arm64':
    os.environ["ARCHFLAGS"] = "-arch arm64"
```

It will look like this:
<img width="729" alt="Screen Shot 2021-01-22 at 12 34 24 PM" src="https://user-images.githubusercontent.com/2423887/105537191-c3902200-5cae-11eb-95e9-a3861f02e8da.png">

Save the setup.py. Then you can build and install it:

```Bash
python3 setup.py build
python3 setup.py install --user
```

or
```
python3 setup.py build
sudo python3 setup.py install
```

Check the Pillow architecture:

```
% lipo -info _imaging.cpython-38-darwin.so 
Architectures in the fat file: _imaging.cpython-38-darwin.so are: x86_64 arm64 
```

You will get Pillow to work with libjpeg. You can do the same setup for  OPENJPEG LIBTIFF etc.

```
--------------------------------------------------------------------
PIL SETUP SUMMARY
--------------------------------------------------------------------
version      Pillow 8.1.0
platform     darwin 3.8.2 (default, Nov  4 2020, 21:23:28)
             [Clang 12.0.0 (clang-1200.0.32.28)]
--------------------------------------------------------------------
--- JPEG support available
*** OPENJPEG (JPEG2000) support not available
--- ZLIB (PNG/ZIP) support available
*** LIBIMAGEQUANT support not available
*** LIBTIFF support not available
*** FREETYPE2 support not available
*** LITTLECMS2 support not available
*** WEBP support not available
*** WEBPMUX support not available
*** XCB (X protocol) support not available
--------------------------------------------------------------------
```

3. Of course, you need the Xcode Command Line Tools


