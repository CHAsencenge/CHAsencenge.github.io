---
title: First Python Code in Windows
date: 2019-07-03 12:39:09  
tags:
- python
categories:
- python-100-practices
---

<blockquote class="blockquote-center">windows下的python程序运行</blockquote>

<!--more-->

## problem：

在CMD中进入python后 使用“python+space+拖拽py文件”的方式运行py文件。

## error：

File "&lt;stdin&gt;", line 1 
    ```python C:\Users\DELL\Desktop\code_py\combination.py``` 
SyntaxError: invalid syntax

## solution:

在__shell脚本__中，运行shell脚本命令；在 __Python命令行__ (就是之前现在CMD中键入python进入的>>>)中，运行Python代码。 
然而，“python combination.py”是一个脚本命令，不是python代码。 
因此，退出python命令行(" __exit()__ ")，直接cd到combination.py所在目录( __如果文件所在目录在根目录内部，直接cd+space+后面的路径，space后面不加“\”__ )，运行python combination.py，即可。




