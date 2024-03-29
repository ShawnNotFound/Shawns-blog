---
title: 排序 | OI笔记
date: 2022-09-28 14:10:50
categories: 信息竞赛
tags: 学习笔记
author: GrainRain
cover: https://pic.imgdb.cn/item/63ba9897be43e0d30e0450f8.jpg
---

# 排序算法
<br>

## 快速排序
特点：将待排序的序列分为左右两段，找出一个标记点x（一般为中间值），将小于x的值放在x左侧的序列，将大于x的值放在x右侧的序列，再递归处理左右两段，因而这是一种稳定的排序方式，平均时间复杂度为O(n · log~2~n)
>注意`平均` 二字，这也就意味着快排的时间复杂度是不稳定的，在最坏的结果下可能会出现O(n^2^)的情况，但出现概率极小
```c ++
#include <iostream>

using namespace std;

const int N = 1e6 + 10;

int a[N];
int n;

void quick_sort(int a[], int l ,int r)
{
	if (l >= r) return;
	
	int mid = a[(l + r) / 2], i = l - 1, j = r + 1; // i - 1 和 j + 1 都是为了处理边界问题
	
	while (i < j)
	{
		do i ++; while (a[i] < mid);
		do j --; while (a[j] > mid); // 不需要判定相等的情况
		if (i < j) swap(a[i], a[j]);
	}
	
	quick_sort (a, l, j);
	quick_sort (a, j + 1, r);
}

int main()
{
	scanf("%d", &n);
	
	for (int i = 0; i < n; i ++)
		scanf("%d", &a[i]);
	
	quick_sort (a, 0, n - 1);
	
	for (int i = 0 ;i < n; i ++)
		printf("%d ", a[i]);
	return 0;
}
```
<br>

## 归并排序
特点：将序列分成两部分分别排序，最后以双指针合并为一个序列。因而归并排序是一种效率高，稳定但占用空间大的排序方式,时间复杂度为O(n · log~2~n)
```cpp
#include <iostream>
using namespace std;

const int N = 1000000;
int n;
int q[N + 10], ans[N + 10];

void merge_sort(int q[], int l, int r)
{
	if (l >= r) return;
	
	int mid = l + r >> 1;
	merge_sort (q, l, mid);
	merge_sort (q, mid + 1, r);
	
	int k = 0, i = l, j = mid + 1;
	while (i <= mid && j <= r)
		if (q[i] <= q[j]) ans[k ++] = q[i ++];
		else ans[k ++] = q[j ++];
		
	while (i <= mid) ans[k ++] = q[i ++];
	while (j <= r) ans[k ++] = q[j ++];
	
	for (int i = l, j = 0; i <= r; i ++, j ++) q[i] = ans[j];
}
int main()
{
	scanf("%d", &n);
	for (int i = 0; i < n; i ++) scanf("%d" ,&q[i]);
	
	merge_sort(q, 0, n - 1);
	
	for (int i = 0; i < n; i ++) printf("%d", q[i]);
	return 0;
}
```
