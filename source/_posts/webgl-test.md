---
title: WebGL Test
date: 2019-05-07 15:35:14
tags: 
- coding
- three.js
- webGL
---

Finally...

{% include_raw '_data/webGLTest.html' %}

It takes me 2 days to have the above demo work in Hexo, via NexT's help.

* use `_data` folder under `source`, this won't be processed by hexo to `public`

* use `{% raw %} {% include_raw '_data/xx.html' %} {% endraw %}` to load raw html files

* still don't know why three.min.js from local not working, had to use via CDNs
