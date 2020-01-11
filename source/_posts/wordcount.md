---
title: Wordcount-Function
date: 2019-07-03 18:22:33
categories:
- blogthings
---

<blockquote class="blockquote-center">blog中添加wordcount功能</blockquote>

<!--more-->

1.在根目录git bash： ```npm install hexo-wordcount --save```

2.在主题配置文件(hexo\themes\next\config.yml)中打开wordcount功能

	# Post wordcount display settings
	# Dependencies: https://github.com/willin/hexo-wordcount
    post_wordcount:
      item_text: true
      wordcount: true
      min2read: true
      totalcount: true
      separated_meta: true

3.修改 hexo\themes\next\layout\post.swig，将下面代码复制到文件中：

    <span title="{{ __('post.wordcount') }}">
         {{ wordcount(post.content) }} 字
    </span>

    <span title="{{ __('post.min2read') }}">
         {{ min2read(post.content) }} 分钟
    </span>