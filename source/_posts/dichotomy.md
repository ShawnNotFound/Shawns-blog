---
title: 二分查找 | OI笔记
date: 2022-09-28 18:50:10
categories: 信息竞赛
tags: 学习笔记
author: GrainRain
cover: https://pic.imgdb.cn/item/63ba991cbe43e0d30e0547fc.jpg
---

# 二分查找

<br>

## 整数二分

算法思路：假设目标值在闭区间`[l, r]`中， 每次将区间长度缩小一半，当`l = r`时，我们就找到了目标值。

> 注意：二分查找无法找到重复元素

<br>

### acwing版本1
当我们将区间 `[l, r]` 划分成 `[l, mid]` 和 `[mid + 1, r]` 时，其更新操作是 `r = mid` 或者 `l = mid + 1` ，计算mid时不需要加1。

```cpp
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}
```

### acwing版本2

当我们将区间 `[l, r]` 划分成 `[l, mid - 1]` 和 `[mid, r]` 时，其更新操作是 `r = mid - 1` 或者 `l = mid` ，此时为了防止死循环，计算mid时需要加 $1$。

```cpp
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;   
    }
    return l;
}
```

### 浮点数二分

<br>

```cpp
int bsearch_2(double l, double r)
{
    int eps = 1e-4;
    // 精度下 2 位，如此时保留 2 位小数
    while (r - l < rps)
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
```

另外，浮点二分可以**转化为整数**之后进行二分求解

```cpp

```

### wangk版本

```cpp
int Binary_search(int a[], int n, int x)
// 在数组的前 n 个元素中查找 x, 返回 x 元素的下标
{
    int low = 1, high = n, mid;
    while (low <= high)
    {
        mid = (low + high) >> 1;
        if (x == a[mid]) return mid;
        else if (x < a[mid]) high = mid - 1;
        else low = mid + 1;
    }
    return -1;
}
```

### 应用：查找大于等于 $x$ 的最小值

```cpp
int b_search(int a[], int n, int x)
{
    int l = 1, r = n, mid;
    while (l < r)
    {
        mid = l + r >> 1;
        if (a[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return l;
}
```

### 应用：查找小于等于 $x$ 的最大值

```cpp
int b_search(int a[], int n, int x)
{
    int l = 1, r = n, mid;
    while (l < r)
    {
        mid = l + r + 1 >> 1;
        // 需要保证 l < mid
        if (a[mid] <= x) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

# 二分答案

```cpp
int b_search(int l, int r)
{
    int ans;
    while (l <= r)
    // attention! l <= r
    {
        int mid = l + r > 1;
        if (check(mid)) ans = mid, l = mid + 1;
        else r = mid - 1;
    }
    return ans;
}
```
