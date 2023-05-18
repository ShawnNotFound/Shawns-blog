---
title: 题目分享(题目部分) | OI
date: 2023-05-18 14:18:21
categories: 信息竞赛
tags: OI
author: Shawn
cover: https://t7.baidu.com/it/u=1670896745,1253501498&fm=193&f=GIF
---

>包含网络流简述，分享的题目和简化题意\
    讲网络流建图 + 网络流建图 + $DP$ ，不讲网络流😅

# 网络流是啥玩意

## 原理

+ ~~我讲不明白~~
+ 不重要，长大以后再学
+ ~~[我已经够大了](https://oi.wiki/graph/flow/)~~

## 实现的功能

+ 在一个有源点 $s$ ( $INF$ )和汇点 $t$ ( $INF$ )的连通图中，有一堆带有最大流量 $w[i]$ 的边，每条边的流量不超过 $w[i]$ ，**网络最大流**可以帮助你求出这个图的**最大流量**是多少

+ 在上一条的基础上，每条边多了一个费用 $c[i]$ 的属性，**最小费用最大流**可以帮助你求出**在这个图为最大流量的前提下**的最小花费

## 板子

+ 网络流的精髓和难点都在建图，因为很少对模板的原理进行考察所以只要会敲板子然后就是普通图论题了

### 最大流(Dinic)

```c++
int d[N], ver[N], nxt[N], edge[N], v[N], head[N], now[N], flag[N][N];
int n, m, s, t, maxflow, tot = 1, u, vv, w;

queue<int> q;

void add(int x, int y, int z)
{
    ver[++ tot] = y, edge[tot] = z, nxt[tot] = head[x], head[x] = tot;
    ver[++ tot] = x, edge[tot] = 0, nxt[tot] = head[y], head[y] = tot;
}

bool bfs()
{
    memset(d, 0, sizeof(d));
    while(q.size())
        q.pop();
    
    q.push(s); d[s] = 1;
    now[s] = head[s];

    while(q.size())
    {
        int x = q.front(); q.pop();
        for(int i = head[x]; i; i = nxt[i])
        {
            int y = ver[i];
            if(!d[y] && edge[i])
            {
                d[y] = d[x] + 1;
                q.push(y);
                now[y] = head[y];
                if(y == t)
                    return 1;
            }
        }
    }
    return 0;
}

int dinic(int x, int flow)
{
    if(x == t)  
        return flow;
    int rest = flow, k, i;
    for(i = now[x]; i && rest; i = nxt[i])
    {
        int y = ver[i];
        now[x] = i;
        if(edge[i] && d[y] == d[x] + 1)
        {
            k = dinic(y, min(rest, edge[i]));
            if(!k)
                d[y] = INF;
            edge[i] -= k;
            edge[i ^ 1] += k;
            rest -= k;
        }
    }
    return flow - rest;
}


signed main()
{

    //建图

    int flow = 0;
    while(bfs())
        while(flow = dinic(s, INF))
            maxflow += flow;
        
    cout << maxflow << endl;

    return 0;
}
```

### 最小费用最大流(spfa + EK)

```c++
bool v[N];
int ver[N], nxt[N], cost[N], head[N], edge[N], incf[N], pre[N], d[N];
int maxflow, ans, u, vv, w, c, n, m, s, t, mincost, tot = 1;
queue<int> q;


void add(int x, int y, int z, int k)
{
    ver[++ tot] = y, edge[tot] = z, cost[tot] = k, nxt[tot] = head[x], head[x] = tot;
    ver[++ tot] = x, edge[tot] = 0, cost[tot] = -k, nxt[tot] = head[y], head[y] = tot;
}

bool spfa()
{
    memset(d, 0x3f, sizeof(d));
    memset(v, false, sizeof(v));
    while(q.size())
        q.pop();
    q.push(s); v[s] = true; d[s] = 0;
    incf[s] = 1 << 30;
    while(q.size())
    {
        int x = q.front(); q.pop(); v[x] = false;
        for(int i = head[x]; i; i = nxt[i])
        {
            if(!edge[i])
                continue;
            int y = ver[i];
            if(d[y] > d[x] + cost[i])
            {
                d[y] = d[x] + cost[i];
                incf[y] = min(incf[x], edge[i]);
                pre[y] = i;
                if(!v[y])
                {
                    q.push(y);
                    v[y] = true;
                }
            }
        }
    }
    if(d[t] == 0x3f3f3f3f)   
        return false;
    return true;
}

void Edmonds_Karp()
{
    int x = t;
    while(x != s)
    {
        int i = pre[x];
        edge[i] -= incf[t];
        edge[i ^ 1] += incf[t];
        x = ver[i ^ 1];
    }
    maxflow += incf[t];
    mincost += d[t] * incf[t];
}


signed main()
{

    //建图

    while(spfa())
        Edmonds_Karp();
    
    cout << maxflow << " " << mincost << endl;

    return 0;
}
```

# P4174

[P4174 [NOI2006] 最大获利](https://www.luogu.com.cn/problem/P4174)，非常基础的一道**最大权闭合图**题

## 简化题意
给定 $N$ 个通讯中转站的建立成本以及 $M$ 个用户群的信息，每个用户群需要使用两个中转站，并且可以获得一定的收益。求选择一些中转站使得净获利最大。


# P3980

[P3980 [NOI2008] 志愿者招募](https://www.luogu.com.cn/problem/P3980)

## 简化题意

+ 在 $M$ 类人中选若干个人，每类人从 $s_i$ 工作到 $t_i$ 天，每一类人有无数个，每个人的价格是 $c_i$，需要用这些人施工 $n$ 天，第 $i$ 天至少需要 $a_i$ 个人，求最小花费


# P2304

[P2304 [NOI2015] 小园丁与老司机](https://www.luogu.com.cn/problem/P2304)

## 题意简化

+ ~~不会简化，太抽象了~~

+ 平面上有 $n$ 棵树，每棵树有一个坐标 $(x_i, x_j)$ ，保证没有两棵树共用一个坐标

### Task1

+ 老司机从 $(0, 0)$ 出发，向符合以下条件的树走
    1. 为左、右、上、左上 $45\degree$、右上 $45\degree$ 五个方向之一。
    2. 沿此方向前进可以到达一棵他尚未许愿过的树。

+ 在无路可走的时候结束，求走最多步数的路线

### Task2

> 大概率不会讲Task2，涉及到有上下界的最小流太抽象了

+ 压路机从原点或一棵树出发，走符合一下条件的路
    1. 只能向上、左上 $45\degree$、右上 $45\degree$ 三个方向之一移动，并且只能在树下改变方向或停止。
    2. 只能经过**可能留下非左右方向车辙印**的地面，但是同一块地面可以被多台轧路机经过。

+ 需要让压路机经过所有可能留下非左右方向车辙印的地面，所需的最小压路机数量
