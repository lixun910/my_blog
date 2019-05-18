---
layout: post
title:  "Inheritance+Encapsulation! Don't copy-n-paste code everywhere in your project"
date:   2019-04-26 14:34:51 -0700
categories: geoda development c++ wxWidgets wxListBox
author: Xun Li
---


In GeoDa, one of the most used UI control is wxListBox, which contains a list of variables of loaded dataset. Users can select one variable from a wxListBox, and the selected variable will be used to created a window (e.g. Histogram, Box plot etc.).

Normally, it is very easy to use wxListBox and it's method `Append()` to add one item or `InsertItems()` to add multiple items in a wxListBox.

If using XRC to create your UI, then you need to add a subclass metadata in the `<object/>`:

{% highlight ruby %}
<object class="wxListBox" name="ID_EXCLUDE_LIST" subclass="GdaListBox">
{% endhighlight %}
