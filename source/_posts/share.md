---
title: 题目分享 | OI
date: 2023-05-15 19:24:43
categories: 信息竞赛
tags: OI
author: Shawn
cover: https://t7.baidu.com/it/u=1260031315,3069314804&fm=193&f=GIF
---

# 网络流是啥玩意

## 原理

+ ~~我讲不明白~~
+ 不重要，长大以后再学
+ ~~[我已经够大了](https://oi.wiki/graph/flow/)~~

## 实现的功能

+ 在一个有源点`s`(INF)和汇点`t`(INF)的连通图中，有一堆带有最大流量`w[i]`的边，每条边的流量不超过`w[i]`，**网络最大流**可以帮助你求出这个图的**最大流量**是多少

+ 在上一条的基础上，每条边多了一个费用`c[i]`的属性，**最小费用最大流**可以帮助你求出**在这个图为最大流量的前提下**的最小花费

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
给定 N 个通讯中转站的建立成本以及 M 个用户群的信息，每个用户群需要使用两个中转站，并且可以获得一定的收益。求选择一些中转站使得净获利最大。

## 题目分析
1. 获得的盈利无法补齐总花费，那么建立这条路就是亏损的\
此时这条路对流量的贡献就是获得的盈利
2. 获得的盈利能补齐总花费，那么就有必要建立这条路\
此时这条路对流量的贡献是花费
3. 以上情况均需要考虑可能有路不止有一条需求
4. 此时分析整个网络的最大流就是（不选的路的盈利 + 选的路的花费），易得使用总盈利-最大流得出的结果就是最大盈利（相减之后`1`情况相当于盈利-盈利无贡献，`2`情况相当于盈利-花费，贡献为净收益）

## 简单示意

![image](https://github.com/ShawnNotFound/Shawns-blog/assets/97796289/6527cb62-fdc6-4bad-813e-382f26ba8cb9)

<p align=center><s>玉桂狗真可爱</s></p>

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

## 简化题意

+ 在 $M$ 类人中选若干个人，每类人从 $s_i$ 工作到 $t_i$ 天，每一类人有无数个，每个人的价格是 $c_i$，需要用这些人施工 $n$ 天，第 $i$ 天至少需要 $a_i$ 个人，求最小花费

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

# P2304

[P2304 [NOI2015] 小园丁与老司机](https://www.luogu.com.cn/problem/P2304)

## 题意简化

+ ~~不会简化，太抽象了~~

+ 平面上又 $n$ 棵树，每棵树有一个坐标 $(x_i, x_j)$ ，保证没有两棵树共用一个坐标

### Task1

+ 老司机从 $(0, 0)$ 出发，向符合以下条件的树走
    1. 为左、右、上、左上 $45\degree$、右上 $45\degree$ 五个方向之一。
    2. 沿此方向前进可以到达一棵他尚未许愿过的树。

+ 在无路可走的时候结束，求走最多步数的路线

### Task2

+ 压路机从原点或一棵树出发，走符合一下条件的路
    1. 只能向上、左上 $45\degree$、右上 $45\degree$ 三个方向之一移动，并且只能在树下改变方向或停止。
    2. 只能经过**可能留下非左右方向车辙印**的地面，但是同一块地面可以被多台轧路机经过。

+ 需要让压路机经过所有可能留下非左右方向车辙印的地面，所需的最小压路机数量

## 思路

+ 参考题解第一篇，~~因为自己写dp部分写抽象了，网络流部分T了~~

+ 先把 $buff$ 叠满
    1. 看见数据范围直接考虑**离散化**
    2. 显然需要预处理出每个树都可以到达哪些树然后记录下来
    3. 惊奇的发现 $y$ 相同的树不超过 $1000$ 颗，所以肯定要把纵坐标相同的树单独拉出来处理

### Task1

+ 首先一眼看出这是 $DP$ ，然后~~就不会了~~

+ 一点一点简化题目，因为 $(0, 0)$ 就是没有贡献的树，所以只要把他看成树最后再减去他的贡献就可以了

+ 然后利用到上面预处理的内容，既然纵坐标有特殊性质且划分出左右非常好处理，所以将纵坐标作为深度，此时在同深度只需要考虑两个方向（左，右）即可

![图示](https://pic2.imgdb.cn/item/6465a1c30d2dde5777d0d798.png)

+ 其实此时已经基本完成了，记录每层的入点和出点（还要记录一个方向），这一层许愿的树就是固定的，然后用 $f[i]$ 表示从 $i$ 进入这一层可以获得的最大收益（走过的树），从 $i$ 分别向左右遍历，转移到下一层即可

### Task2

+ 超纲了，讲不了
