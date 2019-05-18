---
layout: post
title:  "Update libcurl to support TLS 1.2 on Mac OSX 10.7 Lion"
date:   2019-04-27 14:34:51 -0700
categories: geoda development c++ TLS1.2 libcurl 10.7
author: Xun Li
---

The ESRI worldstreetmap doesn’t seem to work in GeoDa since yesterday. This issue is caused by a ArcGIS security update: all connections to ArcGIS will require TLS 1.2 protocols starting on April 16, 2019. (see [https://support.esri.com/en/tlsproducts](https://support.esri.com/en/tlsproducts)). GeoDa uses libcurl to download tiles from ArcGIS. Because GeoDa is compiled and built on Mac OSX 10.7 which doesn’t provide updated OpenSSL library for TLS 1.2, it makes libcurl compilation not supporting TLS 1.2 as well. That’s why current GeoDa can’t get tiles from ESRI anymore.

From libcurl official (see [https://curl.haxx.se/docs/install.html](https://curl.haxx.se/docs/install.html)): to get libcurl to support TLS 1.2, one must build libcurl on Moutain Lion (10.8) or later. However, I was able to work around it on Mac OSX 10.7, so here is a new build of GeoDa 1.12.1.229 that supports TLS 1.2 and ESRI tile works:

OSX: [https://www.dropbox.com/s/3sjvse5t04wgp4i/GeoDa1.12.1.229-Installer.dmg?dl=0](https://www.dropbox.com/s/3sjvse5t04wgp4i/GeoDa1.12.1.229-Installer.dmg?dl=0)

GeoDa also have other features that will reply on TLS 1.2 e.g. submit bug reports to github, connection to Carto etc., so I suggest the long term solution is moving our building platform to Moutain Lion (10.8). Purchase link: [https://www.apple.com/shop/product/D6377Z/A/os-x-mountain-lion](https://www.apple.com/shop/product/D6377Z/A/os-x-mountain-lion)

On windows platform,  TLS 1.2 has been updated in Windows 7 in SP1, see: [https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi). Therefore, GeoDa Windows version doesn’t have this issue.

Let me know any questions. Thanks!
