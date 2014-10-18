---
title: "github博客的Markdown编辑文件中插入数学公式"
layout: post
comments: yes
---
经试验,可以使用[forkosh](http://www.forkosh.com)

forkosh上提供了关于Latex公式的一份简短而很有用的帮助，参考[LaTeX Math Tutorial](http://www.forkosh.com/mathtextutorial.html).

使用forkosh插入公式的方法是

< img src="http://www.forkosh.com/mathtex.cgi? 在此处插入Latex公式" >

> 为了以文本的形式显示上面的html语句,需要在"<"和"img"之间有个空格. 原本,想通过反斜杠对"<"转义,在本地调试转义成功,但上传github后却不行. 特此备注.

给个例子:

< img src="http://www.forkosh.com/mathtex.cgi? \Large x=\frac{-b\pm\sqrt{b^ 2-4ac}}{2a}" >

显示结果为：
<img src="http://www.forkosh.com/mathtex.cgi? \Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}">

因为网页插入公式的原理是调用“forkosh网站的服务器”动态生成的，所有保证公式正常显示的前提是该网址能一直存在着.

更多公式表达式以后逐步附上: