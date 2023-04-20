---
title: OI新发现 | OI笔记
date: 2023-03-11 20:40:32
categories: 信息竞赛
tags: OI骗分
author: Shawn
cover: https://img2.baidu.com/it/u=1190886353,2002708477&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
---

# OI新发现

## 高速查找二进制中1的个数
```c++
int a;
cin >> a;
cout << __builtin_popcount(a) << endl;
```
+ 无需任何头文件，OI可用
+ 时间复杂度$O(\log{\log{n}})$或$O(1)$，绝对高于手写
+ 香的，嘶溜嘶溜~
