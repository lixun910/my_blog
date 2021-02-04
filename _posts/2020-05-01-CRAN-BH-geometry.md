---
layout: post
title: CRAN BH Geometry Compiling Warning on R 3.6 
date:   2020-05-01 13:50:39
categories: CRAN R BH
author: Xun Li
---

For CRAN checkint with R-oldrelease, there could be an issue if you use Boost.Geometry with the following environment:

* R version 3.6.3 (2020-02-29)
* x86_64-w64-mingw32 (64-bit) with gcc-4.9.3
* BH_1.75.0-0

It is because Boost 1.75.0 requires C++14 for Boost.Geometry. See https://www.boost.org/users/history/version_1_75_0.html

```
Geometry:
WARNING: Following the deprecation notice of C++03 issued with Boost 1.73, from now on the Boost.Geometry requires a capable C++14 compiler.
```

However, C++14 is not avaiable with gcc-4.9.3 on x86_64-w64-mingw32 (64-bit). See https://gcc.gnu.org/projects/cxx-status.html
	
```
GCC has full support for the of the 2014 C++ standard.

This mode is the default in GCC 6.1 up until GCC 10 (including); it can be explicitly selected with the -std=c++14 command-line flag, or -std=gnu++14 to enable GNU extensions as well.
```

It is also not possible to install BH < 1.73 for CRAN check. 
