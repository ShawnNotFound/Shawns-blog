---
title: 模拟退火 | OI笔记
date: 2023-04-01 21:39:16
categories: 信息竞赛
tags: 学习笔记
author: Shawn
cover: https://t7.baidu.com/it/u=2860139544,3309382475&fm=193&f=GIF
---
# 模拟退火介绍
## 前提
+ 函数必须具有连续性
# 3167 星星还是树
## 题意
+ 给定一些点，求二维费马点
## 费马点
+ 在平面上到所有点距离之和最小的点
### 一维
排序找中位数即可
### 二维
三分套三分求解**AcWing1420**, 证明函数为下凸函数
## 模拟退火做法
### 情况
+ 在不知道此函数为下凸函数时，可以使用模拟退火求解
> simutate anneal  模拟退火
### 思路
1. 随机一个初始点
2. 定义步长`T`为`10000`(数据范围)，结束为`T <= 0.0001`(`1e-4`)
3. 计算`delta`并且判断是否跳点
4. 重复循环以上步骤若干次，可以结合**卡时**

### 代码
```c++
#include<iostream>
#include<cmath>
#include<algorithm>
#include<cstring>
#include<ctime>
#include<iomanip>//cout设置保留位数用

#define x first
#define y second
//方便读入

const int INF = 1e8;
const int N = 110;

using namespace std;

typedef pair<double, double> PDD;

int n;
double ans = INF;
PDD p[N];

//获取一个l -> r之间的随机数
double get_rand(double l, double r)
{
    return (double)rand() / RAND_MAX * (r - l) + l;
    //            ->生成0-1中的一个数<- 乘上r, l的差值保证在范围之内，再加上l获得最终结果
}


//计算两点之间的距离
double get_dist(PDD a, PDD b)
{
    double dx = a.x - b.x;
    double dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
}

//计算某个点作为答案时的长度
double calc(PDD a)
{
    double res = 0;

    //依次加上所有点到当前点的距离
    for(int i = 1; i <= n; i ++)
    {
        res += get_dist(a, p[i]); 
    }

    ans = min(ans, res);//更新全局最小值

    return res;
}


//模拟退火主函数
void simulate_anneal()
{
    //获得当前点cur,对应的x, y均取全集中的随机数
    PDD cur(get_rand(0, 10000), get_rand(0, 10000));

    //按照温度（步长）循环遍历猜答案
    for(double t = 10000; t > 1e-4; t *= 0.99)
    {
        PDD np(get_rand(cur.x - t, cur.x + t), get_rand(cur.y - t, cur.y + t));//在范围内随机选出下一个点
        double dx = calc(np) - calc(cur);//当前点和下一个点的差值（delta）
        if(exp(-dx / t) > get_rand(0, 1))//就是这么写的
            cur = np;//跳点
    }
}

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++)
    {
        cin >> p[i].x >> p[i].y;
    }
    for(int i = 1; i <= 100; i ++) //尝试100次并不断更新最小值以尽量获得最小值
        simulate_anneal();
    
    cout << fixed << setprecision(0) << ans << endl;

    return 0;
}
```

# 2436.保龄球
>思路比较类似，有空补
## 代码
```c++
#include<iostream>
#include<cstring>
#include<cmath>
#include<algorithm>

#define x first
#define y second

const int N = 55;

using namespace std;

typedef pair<int, int> PII;

PII q[N];

int n, m, ans;

int calc()
{
    int res = 0;
    for(int i = 1; i <= m; i ++)
    {
        res += q[i].x + q[i].y;
        if(i <= n)
        {
            if(q[i].x == 10)
                res += q[i + 1].x + q[i + 1].y;
            else if(q[i].x + q[i].y == 10)
                res += q[i + 1].x;
        }
    }
    ans = max(ans, res);
    return res;
}

void simulate_anneal()
{
    for(double t = 1e4; t > 1e-4; t *= 0.99)
    {
        int a = rand() % m + 1, b = rand() % m + 1;
        int x = calc();
        swap(q[a], q[b]);
        if(n + (q[n].x == 10) == m)
        {
            int y = calc();
            int delta = y - x;
            if(exp(delta / t) < (double)rand() / RAND_MAX)  
                swap(q[a], q[b]);
        }
        else
            swap(q[a], q[b]);
    }
}

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++)    
        cin >> q[i].x >> q[i].y;
    
    if(q[n].x == 10)
    {
        m = n + 1;
        cin >> q[m].x >> q[m].y;
    }
    else
        m = n;
    
    while((double)clock() / CLOCKS_PER_SEC < 0.8)
        simulate_anneal();

    cout << ans << endl;

    return 0;
}


```
