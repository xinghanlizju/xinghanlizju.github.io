---
title: msckf-ba
categories: working
tags: SLAM
---
# MSCKF
{% codeblock %}
预备知识：卡尔曼滤波器
{% endcodeblock %}

卡尔曼滤波器设计之初是想提出一组最小均方误差的估计器，其假设之一是线性动态系统和线性观测。解决非线性观测和非线性动态的问题比较常见的思路是通过扩展卡尔曼滤波器来解决。

然而单个状态的非线性化将线性化的误差引入。