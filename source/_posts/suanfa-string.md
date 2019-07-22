---
title: 反转单词
date: 2019-07-11 23:31:32
tags: ['js数据结构与算法']
categories: js数据结构与算法
---
### 反转单词
<!-- more -->

![image](/images/suanfa-string.jpg)
思路：
1. 字符串按空格进行分隔，保存数组，数组的元素的先后顺序就是单词的顺序
2. 对数组进行遍历，然后每个元素进行反转

```
function reverseString(str) {
    return str.split(' ').map(item => {
        return item.split('').reverse().join('')
    }).join(' ')
}
```