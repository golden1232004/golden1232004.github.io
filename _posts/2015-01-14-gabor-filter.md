---
title: "gabor filter"
layout: post
comments: yes
---
1. 简介

　   Gabor变换属于加窗傅立叶变换，Gabor函数可以在频域不同尺度、不同方向上提取相关的特征。Gabor 滤波器的频率和方向类似于人类的视觉系统，所以常用于纹理识别。在空间域，二维Gabor滤波器是一个高斯核函数和正弦平面波的乘积

2. 公式

   复数表示:
<img src="http://www.forkosh.com/mathtex.cgi? \Large g(x,y,\lambda,\theta,\psi,\sigma,\gamma) = exp\left( -\frac{x'^2+\gamma^2y'^2}{2\sigma^2}  \right)exp\left( i\left( 2\pi\frac{x'}{\lambda}+\psi \right) \right)">

   实部表示:
<img src="http://www.forkosh.com/mathtex.cgi? \Large g(x,y,\lambda,\theta,\psi,\sigma,\gamma) = exp\left( -\frac{x'^2+\gamma^2y'^2}{2\sigma^2}  \right)cos\left( 2\pi\frac{x'}{\lambda}+\psi \right)">

   虚部表示:
<img src="http://www.forkosh.com/mathtex.cgi? \Large g(x,y,\lambda,\theta,\psi,\sigma,\gamma) = exp\left( -\frac{x'^2+\gamma^2y'^2}{2\sigma^2}  \right)sin\left( 2\pi\frac{x'}{\lambda}+\psi \right)">

   其中:
<img src="http://www.forkosh.com/mathtex.cgi? \Large x' = xcos\theta+ysin\theta, y'=-xsin\theta+ycos\theta ">
<img src="http://www.forkosh.com/mathtex.cgi? \Large \lambda: "> 正弦函数的波长
<img src="http://www.forkosh.com/mathtex.cgi? \Large \theta: "> gabor核函数的方向
<img src="http://www.forkosh.com/mathtex.cgi? \Large \psi: "> 正弦函数的相位偏移
<img src="http://www.forkosh.com/mathtex.cgi? \Large \sigma: "> 高斯函数的标准差
<img src="http://www.forkosh.com/mathtex.cgi? \Large \gamma: "> 空间的宽高比

> 在实际应用中取实部表示,忽略虚部表示

   
