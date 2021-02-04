---
layout: post
title: Build libgdal 2.4.4 on Apple M1 Silicon
date:   2021-01-01 13:50:39
categories: libgdal Apple M1 Silicon
author: Xun Li
---

The gdal version 2.4.4 is working without any problem on Apple M1 Silicon with the following dependencies:

* proj-7.2.1
* geos-3.9.0
* tiff-4.2.0
* sqlite-autoconf-3340000
* libspatialite-5.0.0
* freexl-1.0.6
* postgresql-13.1
* zlib: https://github.com/madler/zlib.git
* zstd: https://github.com/facebook/zstd.git
* jpeg-8
* lzma: https://github.com/kobolabs/liblzma.git
* xml2: https://gitlab.gnome.org/GNOME/libxml2.git 

## libgdal 2.4.4

NOTE: when compile gdal from source, you might need to manually change the `GDALmake.opt` file to make sure the libs and headers of dependent libraries are correctly configured. 

```
libgdal.20.dylib:
	/Users/xun/Downloads/lib/libgdal.20.dylib (compatibility version 26.0.0, current version 26.4.0)
	@executable_path/../Frameworks/libcrypto.1.1.dylib (compatibility version 1.1.0, current version 1.1.0)
	@executable_path/../Frameworks/libproj.14.dylib (compatibility version 14.0.0, current version 14.0.2)
	@executable_path/../Frameworks/libsqlite3.0.dylib (compatibility version 9.0.0, current version 9.6.0)
	@executable_path/../Frameworks/libjpeg.8.dylib (compatibility version 9.0.0, current version 9.0.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1292.60.1)
	@executable_path/../Frameworks/libspatialite.5.dylib (compatibility version 6.0.0, current version 6.1.0)
	@executable_path/../Frameworks/libcurl.4.dylib (compatibility version 12.0.0, current version 12.0.0)
	/usr/lib/libiconv.2.dylib (compatibility version 7.0.0, current version 7.0.0)
	@executable_path/../Frameworks/libxml2.2.9.10.dylib (compatibility version 0.0.0, current version 2.9.10)
	/usr/lib/libicucore.A.dylib (compatibility version 1.0.0, current version 66.1.0)
	@executable_path/../Frameworks/libpq.5.dylib (compatibility version 5.0.0, current version 5.13.0)
	@executable_path/../Frameworks/libz.1.dylib (compatibility version 1.0.0, current version 1.2.11)
	/usr/lib/libedit.3.dylib (compatibility version 2.0.0, current version 3.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 904.4.0)
```

## libproj 5.2.0

```Bash
cd proj-5.2.0
mkdir bld
cd bld
LDFLAGS="-arch arm64" cmake -DBUILD_SHARED_LIBS=OFF -DCMAKE_INSTALL_PREFIX=/usr/local -DCMAKE_BUILD_TYPE=Release  ..
make
make install
```


libspatialite
```
libspatialite.5.dylib:
	libspatialite.5.dylib (compatibility version 6.0.0, current version 6.1.0)
	@executable_path/../Frameworks/libssl.1.1.dylib (compatibility version 1.1.0, current version 1.1.0)
	@executable_path/../Frameworks/libcrypto.1.1.dylib (compatibility version 1.1.0, current version 1.1.0)
	@executable_path/../Frameworks/libfreexl.1.dylib (compatibility version 3.0.0, current version 3.0.0)
	/usr/lib/libiconv.2.dylib (compatibility version 7.0.0, current version 7.0.0)
	@executable_path/../Frameworks/libproj.14.dylib (compatibility version 14.0.0, current version 14.0.2)
	@executable_path/../Frameworks/libcurl.4.dylib (compatibility version 12.0.0, current version 12.0.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1292.60.1)
	@executable_path/../Frameworks/libsqlite3.0.dylib (compatibility version 9.0.0, current version 9.6.0)
	@executable_path/../Frameworks/libz.1.dylib (compatibility version 1.0.0, current version 1.2.11)
	@executable_path/../Frameworks/libgeos_c.1.dylib (compatibility version 18.0.0, current version 18.2.0)
	@executable_path/../Frameworks/libgeos-3.9.0.dylib (compatibility version 0.0.0, current version 0.0.0)
```

libgeos_c.dylib
```
libgeos_c.1.dylib:
	libgeos_c.1.dylib (compatibility version 18.0.0, current version 18.2.0)
	@executable_path/../Frameworks/libgeos-3.9.0.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/lib/libc++.1.dylib (compatibility version 1.0.0, current version 904.4.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1292.60.1)
```

NOTE: to `install_name_tool` change on libgeos_c.dylib,  there will be `codesign` warning. Here is a workaround:

```Bash
install_name_tool -id libgeos_c.1.dylib build/GeoDa.app/Contents/Frameworks/libgeos_c.1.dylib
codesign -s - -f build/GeoDa.app/Contents/Frameworks/libgeos_c.1.dylib
install_name_tool -change "/Users/xun/github/geoda/BuildTools/macosx/libraries/lib/libgeos-3.9.0.dylib" "@executable_path/../Frameworks/libgeos-3.9.0.dylib" build/GeoDa.app/Contents/Frameworks/libgeos_c.1.dylib
codesign -s - -f build/GeoDa.app/Contents/Frameworks/libgeos_c.1.dylib
```
