---
title: ST 表 | OI笔记
date: 2023-1-28 23:01:57
categories: 信息竞赛
tags: 数据结构
author: Shawn  GrainRain
cover: 
https://t7.baidu.com/it/u=821791764,3584359573&fm=193&f=GIF
---

# $ST$ 表

> RMQ ($Range\ Minimum\ /\ Maximum\ Query$) 问题：
> $O(1)$ 查询区间最大 / 最小值

## 基本思想：

定义 `f[i][j]` 表示从 $i$ 开始，长度为 $2^j$ 的区间中最大 / 小值

考虑状态转移，将一个区间分为两部分，去两部分的 $max$ 或 $min$ 即可。则有：

$$ f[i, j] = max([i, j - 1],\ [i + 2^{j - 1}, j - 1]) $$

![](https://pic.imgdb.cn/item/63d4c5c6face21e9efa1b86b.jpg)

在具体使用时，询问 $[l, r]$ 区间，`len = r - l + 1`，只需找到最大的 $k$ 使得 $2^k \geqslant len$。

$$\Kappa = \lfloor \frac {log(len)} {log(2)} \rfloor $$

(c++ 标准库中 $log$ 函数默认以 $e$ 为底)

## 时间复杂度分析

$f[i][j]$ 第一维 $n$ 个，第二维 $log_2n$ 个。
因此预处理时间复杂度为 $O(n·log_2n)$，查询时间复杂度为 $O(1)$

## 例题

[Acwing1273 - 天才的记忆](https://www.acwing.com/problem/content/1275/)

```cpp
const int N = 2e5 + 10, J = 18;

int n, m;
int w[N];

int f[N][J];

void init()
{
	for (int j = 0; j < J; j ++)
		for (int i = 1; i + (1 << j) - 1 <= n; i ++)
			if (!j) f[i][j] = w[i];
			else f[i][j] = max(f[i][j - 1], f[i + (1 << j - 1)][j - 1]);
}
// 初始化 st 

int ask(int l, int r)
{
	int len = r - l + 1;
	int k = log(len) / log(2);
	return max(f[l][k], f[r - (1 << k) + 1][k]);
}

int main()
{
	cin >> n;
	for (int i = 1; i <= n; i ++)
		cin >> w[i];
	init();
	cin >> m;
	for (int i = 1; i <= m; i ++)
	{
		int l, r;
		cin >> l >> r;
		cout << ask(l, r) << endl;
	}
	return 0;
}
```
