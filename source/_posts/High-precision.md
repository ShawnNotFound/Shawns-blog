---
title: 高精度 | OI笔记
date: 2022-09-29 15:21:42
categories: 信息竞赛
tags: 学习笔记
author: GrainRain
cover: https://pic.imgdb.cn/item/63ba99bebe43e0d30e064975.jpg
---

# 高精度运算
## 基本思想
以字符串 / 字符数组存储大整数
模拟人类计算过程按位计算
重点和难点是判断进位以及借位的边界问题

## English Time !

> add [æd] v. 加
> subtract [səbˈtrækt] v. 减
> multiply [ˈmʌltɪplaɪ] v. 乘
> divide [dɪˈvaɪd] v. 除

***

## 读入与存储

```cpp
	string a, b;
	vector<int> A, B;
	cin >> a >> b;
	// 以字符串形式读入
	for (int i = a.size() - 1; i >= 0; i --) 
		A.push_back(a[i] - '0');
	for (int i = b.size() - 1; i >= 0; i --)
		B.push_back(b[i] - '0');
	// 注意：要以逆序读入，为方便处理进位产生的问题
	
	auto C = add(A, B); // 高精度加法
	auto C = sub(A, B); // 高精度减法
	auto C = mul(A, b) // 高精度乘法
	auto C = div(A, b, t)// 高精度除法, t 为余数
	
	for (int i = C.size() - 1; i >= 0; i --)
		printf("%d", C[i]);
	// 逆序输出
```

## 高精度加法 (add)

```c++
vector<int> add(vector<int> &A, vector<int> &B)
{
	vector<int> C;
	int t = 0;
	// 存储当前一位以及产生的进位
	for (int i = 0; i < A.size() || i < B.size(); i ++)
	{
		if (i < A.size()) t += A[i];
		if (i < B.size()) t += B[i];
		C.push_back(t % 10);
		t = t / 10;
	}
	if (t) C.push_back(1);
	return C;
}
```

## 高精度减法 (sub)

>要求：A > 0 && B > 0
如果：A >= B 计算 A - B
否则：A < B 计算 -(B - A)

```c++
bool cmp(vector<int> &A, vector<int> &B)
{
	if (A.size() != B.size())
		return A.size() > B.size();
	for (int i = A.size() - 1; i >= 0; i --)
		if (A[i] != B[i])
			return A[i] > B[i];
	
	return true;
}
// 判断A是否大于等于B，是则返回true，否则返回false 

vector<int> sub(vector<int> &A, vector<int> &B)
{
	vector<int> C;
	for (int i = 0, t = 0; i < A.size(); i ++)
	{
		// 处理借位问题 
		t = A[i] - t;
		if (i < B.size()) t -= B[i];
		C.push_back((t + 10) % 10);
		// 如果t > 0, 返回t; 如果t < 0, 返回t + 10;
		if (t < 0) t = 1;
		else t = 0;
		// 标记借位
	}
	while (C.size() > 1 && C.back() == 0)
		C.pop_back();
	// 去掉前导0 
	return C;
}
```

高精度减法输出需要特殊判定得数为负数的情况

```c++
	if (cmp(A, B))
	{
		auto C = sub(A, B);
		for (int i = C.size() - 1; i >= 0; i --)
			printf("%d", C[i]);
	}
		
	else
	{
		auto C = sub(B, A);
		cout << '-';
		for (int i = C.size() - 1; i >= 0; i --)
		printf("%d", C[i]);
		
	}
```

## 高精度乘法(mul)

注意：高精度乘法和手撕的乘法有所差别，比如说`A * b`， 是将`A`的每一位与`b`相乘，而不是将`A`的每一位与`b`的每一位相乘再相加
```c++
vector<int> mul(vector<int> &A, int b)
{
	vector<int> C;
	int t = 0;
	// 表示当前数位上的值并处理进位问题
	 
	for (int i = 0; i < A.size() || t; i ++)
	{
		if (i < A.size())
			t += A[i] * b;
		C.push_back(t % 10);
		t /= 10;
		// 处理进位 
	}
	while (C.size() > 1 && C.back() == 0)
		C.pop_back();
	// 处理多位数 * 0 导致 C 中出现多位0的情况 
	
	return C;
}
```

## 高精度除法(div)

注意：除法需要多定义一个变量 $t$ 来存储除不尽的余数

```c++
vector<int> div(const vector<int> &A, int b, int &r)
// r 返回无法整除时的余数
{
	vector<int> C;
	r = 0;
	// 表示在当前数位上等待做除法运算的值
	for (int i = A.size() - 1; i >= 0; i --)
	{
		r = r * 10 + A[i];
		C.push_back(r / b);
		r = r % b;
	}
	
	reverse(C.begin(), C.end());
	while (C.size() > 1 && C.back() == 0)
		C.pop_back();
	
	return C;
}
```

> 为返回余数的值， 需要在基础代码上增加以下几行：

```c++
int r;
auto C = div(A, b, r);
cout << endl << r << endl;;
```


## 扩展：两个高精度数的大小比较

基本思想：先比较两个高精度数的位数，如果位数不同直接返回即可，如果位数相同，从最高位使用贪心的思想比较每一位，直到第1位

```cpp
bool cmp(vector<int> &A, vector<int> &B)
{
	if (A.size() != B.size()) return A.size() > B.size();
	else
	{
		for(int i = 0; i < A.size(); i ++)
			if (A[i] > B[i]) return 1;
		return 0;
	}
}
// 前一个数比后一个数大则返回 1 ，否则返回 0 
```
