---
layout: post
title: Brush-and-linking plots
date:   2019-07-02 13:50:39
categories: brush plots maps 
author: Xun Li
---

# Brush-linking on large big dataset

(tens of millions)

### Some tricks

### 1. Memory

#### TypedArray

TypedArray takes less memory than array and is gabbage-collection-friendly. 

>A TypedArray object describes an array-like view of an underlying binary data buffer. There is no global property named TypedArray, nor is there a directly visible TypedArray constructor. 

In practice, one can create a ArrayBuffer with a Int32Array view referring to the buffer:

```javascript
const buffer = new ArrayBuffer(8); // 8 bytes
const view = new Int32Array(buffer); // int32 takes 4 bytes
// so the length of view is 2
console.log(view.length);
// manipulate Int32Array like list
view[0] = 10;
// Since `view` is a `reference` of the underlying bytearray 
// the change actually happens on `buffer`
// > buffer
//   ArrayBuffer(8) {}
//   [[Int8Array]]: Int8Array(8) [10, 0, 0, 0, 0, 0, 0, 0]
//   [[Int16Array]]: Int16Array(4) [10, 0, 0, 0]
//   [[Int32Array]]: Int32Array(2) [10, 0]
//   [[Uint8Array]]: Uint8Array(8) [10, 0, 0, 0, 0, 0, 0, 0]
```

## 2. Streaming data

Load data streamly from WebSocket instead of a one-time bulk-load, so rendering can be progressive and incremental. 



## 3. WebGL-2D

https://github.com/gameclosure/webgl-2d

Switching your Canvas2D sketch to a WebGL2D to gain performance.

https://www.npmjs.com/package/gl-plot2d

A rendering engine for drawing huge 2D plots using WebGL.


## Issues:

Mouse operations in brush-linking on WebGL 3D space is not trivial. E.g. how to detect which objects been selected when making a selection box on screen (which is 2D)