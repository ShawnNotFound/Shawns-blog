---
title: 余数定理 | OI笔记
date: 2022-12-4 07:04:34
categories: 信息竞赛
tags: 学习笔记
author: GrainRain
cover: https://pic.imgdb.cn/item/63ba9f76be43e0d30e103609.jpg
---

# 余数加法定理

`(a + b) % c = (a % c + b % c) % c`

**证明：**

若 `a = xc + p`, `b = yc + q`
则 `a % c = p`, `b % c = q`

则有:
`(a + b) % c = [(x + y)c + p + q] % c = (p + q) % c`
`(a % c + b % c) % c = (p + q) % c`

# 余数乘法定理

`(a * b) % c = (a % c * b % c) % c`

**证明**

若 `a = xc + p`, `b = yc + q`
则

# 同余定理

a和b同余：a ||| b
