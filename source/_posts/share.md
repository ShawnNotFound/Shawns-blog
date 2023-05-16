---
title: 题目分享 | OI
date: 2023-05-15 19:24:43
categories: 信息竞赛
tags: OI
author: Shawn
cover: https://t7.baidu.com/it/u=1260031315,3069314804&fm=193&f=GIF
---

# P4174

[P4174 [NOI2006] 最大获利](https://www.luogu.com.cn/problem/P4174)，非常基础的一道**最大权闭合图**题

## 简化题意
给定 N 个通讯中转站的建立成本以及 M 个用户群的信息，每个用户群需要使用两个中转站，并且可以获得一定的收益。求选择一些中转站使得净获利最大。

## 题目分析

![image](https://github.com/ShawnNotFound/Shawns-blog/assets/97796289/4c0c8022-efb1-4ef9-aa85-60eec10447cd)


## 代码实现

```c++
input(n, m);
s = n + m + 1;
t = n + m + 2;

for(int i = 1; i <= n; i ++)
{
    int cost;
    input(cost);

    add(i, t, cost);
}

for(int i = 1; i <= m; i ++)
{
    int from, to, w;
    input(from, to, w);

    totcost += w;
    
    add(s, i + n, w);
    
    add(i + n, from, w);
    add(i + n, to, w);
}
```

# P3980

[P3980 [NOI2008] 志愿者招募](https://www.luogu.com.cn/problem/P3980)

## 错误方法

+ 将原点连向每一个人(INF, c[i])， 人连向对应的工作天数(INF, 0)，工作天数连向汇点(need, 0)

+ 错误原因：\
可能出现选择某一个人，导致某一天工作量溢出，但是价格低于恰好选择的价格

## 分析

+ 如果想让每天工作量可以溢出，就需要连接容量为INF的边，但是保持“二分图式连法”就会导致每一天都要跑慢INF，不可能实现
+ 对于天数问题，还有一种常用建图方式——链状图
+ 如果将天数连成一条长链，然后用INF填充源点汇点，并用免费的INF-need填充天与天之间的边，最后用每一个人的边补全流量，让最大流保持为INF即为正解
+ 分析正确性：程序会尽可能选择免费的边，至少需要使用need个志愿者来完成当天的工作（不然是不可能补齐INF的），但是如果有更优策略可以放弃免费边走付费边

## 正确方法

1. 构造一个链状的图，从`s`开始向第一天连边(INF, 0)，然后顺次链接每一天(INF - need, 0)，最后再连接最后一天到`t`的边(INF, 0)
2. 将每个人对应一条从这个人开始工作的天数到结束工作**之后的一天**的边(max(need[l ~ r]), 0)或直接连接(INF, 0)

## 代码实现

```c++
cin >> n >> m;
s = n + m + 1, t = n + m + 2;

add(s, 1, INF, 0);

for(int i = 1; i <= n ; i ++)
{
    int nd;
    cin >> nd;

    need[i] = nd;

    add(i, i + 1, INF - nd, 0);
}

add(n + 1, t, INF, 0);

for(int i = 1; i <= m; i ++)
{
    int ss, tt, c;
    cin >> ss >> tt >> c;

    int maxn = getmax(ss, tt);

    add(ss, tt + 1, maxn, c);
}
```

