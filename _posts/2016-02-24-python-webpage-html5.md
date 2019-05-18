---
layout: post
title: Let Python talks with Webpages (back and forth)
date:   2015-02-24 13:50:39
categories: python HTML5 WebSocket
author: Xun Li
---

In the 2015 SigSpatial conference (Seattle, WA), I've demonstrated a prototype that allow the python developers can visualize their maps in a web browser. The cool things are, in a python terminal, the developers can not only type "show_map" to pop-up a webpage draws the map (using `HTML5 canvas`), but also can get what has been selected on the map (e.g. if you use mouse select on map). Besides, the webpages can also communicate with each other, the selection, for example, can be synchronized across all opened webpages. (See the video demo below).

![my alternate text](/assets/images/python-webpage.png)


The technical behind is `web socket`: there is a web socket server (written in `Python`) plus a simple python HTTP server running in the background. The "d3viz.setup()" you seen in the demo is actually doing the setup. So, the python code can exchange information with HTML webpages via "web socket".

This can be easily extended to `R` or other scripting language, so you will only have one piece of HTML5 canvas based visualization code, which can be called to draw maps in different scripting languages. The most important thing is the developers in either Python or R world don't need to spend a long time to figure out how to draw the right map or plot (if you are not a fun of matplotlib). The worse thing is these maps and plots you draw don't even link (e.g. you select on the pie chart, you are expecting what have been selected shown on the map as well).


<iframe width="100%" height="315" src="https://www.youtube.com/embed/5hNDkSBfs8s" frameborder="0" allowfullscreen></iframe>
