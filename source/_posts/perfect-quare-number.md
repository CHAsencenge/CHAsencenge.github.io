---
title: perfect-quare-number
date: 2019-07-04 17:48:04
tags:
- python
categories:
- python-100-practices
---

<blockquote class="blockquote-center">求一个数，加100和加268都是完全平方数<br>解决:unindent does not match any outer indentation level问题
</blockquote>

<!--more-->

## code
```python
import math
for i in range(100000):
	x = int(math.sqrt(i + 100))
	y = int(math.sqrt(i + 268))
	if (x * x == i + 100) and (y * y == i + 268):
		print (i)
```

## error
unindent does not match any outer indentation level：Tab换行对齐和四个空格的对齐不能混用，混用会这样报错。

print i：正确应为 print (i)

