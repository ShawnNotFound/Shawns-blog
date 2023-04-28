---
title: 基础数论 | OI笔记
date: 2023-04-28 20:39:16
categories: 信息竞赛
tags: 学习笔记
author: Shawn
cover: https://t7.baidu.com/it/u=1871347157,3677968557&fm=193&f=GIF
---
# 最大公约数(gcd)

>使用辗转相除法求解
```c++
int gcd(int a, int b)
{
    if(b == 0)
        return a;
    return gcd(b, a % b);
}
```
或简化写法
```c++
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
    //or return b == 0 ? a : gcd(b, a % b);
}
```

# 最小公倍数(lcm)

## 原理

+ **最小公倍数**可理解为**去除两个数一个公共因数**之后的乘积
+ **最大公因数**可理解为**两个数公共因数**的乘积
+ 这样就容易得到两者乘积为这两个数的乘积

$$\text{gcd}(a, b) * \text{lcm}(a, b) = a * b$$

+ 由此即可得到 
$$\text{lcm}(a, b) = \frac{a * b}{\text{gcd}(a, b)}$$

## 代码实现

```c++
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
}

int lcm(int a, int b)
{
    return a * b / gcd(a, b);
}
```


# 素数

## 线性筛
```c++
int primes[N], cnt; //primes存储所有素数
bool st[N]； //st[x]存储x是否被筛掉

void get_primes(int n)
{
    for(int i = 2; i <= n; i ++)
    {
        if(!st[i])
            primes[cnt ++] = i;
        for(int j = 0; primes[j] <= n / i; j ++)
        {
            st[primes[j] * i] = true;
            if(i % primes[j] == 0)
                break;
        }
    }
}
```

# 快速幂

>求$m ^ k \pmod p$，时间复杂度为$O(\log{k})$

```c++
int qpow(int m, int k, int p)
{
    int res = 1 % p, t = m;

    while(k)
    {
        if(k & 1)
            res = res * t % p;
        t = t * t % p;
        k >>= 1;
    }

    return res;
}
```
