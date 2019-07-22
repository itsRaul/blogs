---
title: 数组类
date: 2019-07-12
tags: ['js数据结构与算法']
categories: js数据结构与算法
---
### 数组类
<!-- more -->

### 电话号码的字母组合
![image](/images/array.jpg)

思路：
1. "23"分别对应'abc','def',把"23"分隔split成数组对应字母，遍历，splice替换，判断是否还有组合，递归
2. "234" 的时候，先算出23的组合，23的新组合和4进行组合，两两计算
![image](/images/array1.jpg)

![image](/images/letterCombinations.jpg)

### 种花问题
![image](/images/array2.jpg)

思路：
1. 边界处理，判断1左右两边是否是0，如：0，1，0
2. 定义计数器0
3. 遍历，判断是否是0为空地
4. 再判断第一个同时第二个是否为0空地，是的max++,跳到下一个进行左右判断

![image](/images/canPlaceFlowers.jpg)