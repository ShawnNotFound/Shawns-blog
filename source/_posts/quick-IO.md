---
title: 快读快写 | OI笔记
date: 2023-03-02 16:08:06
categories: 信息竞赛
tags: 学习笔记
author: Shawn
cover: https://t7.baidu.com/it/u=2067359777,945248471&fm=193&f=GIF
---
# 快读快写模板
## 普通简版
```c++
#include<iostream>
#include<cctype>

using namespace std;

template <typename T>
inline void input(T &x)
{
	x = 0; char c = getchar();
	for(; !isdigit(c); )
		c = getchar();
	for(; isdigit(c); c = getchar())
		x = x * 10 + (c ^ '0'); 
}

template <typename T>
inline void output(T x, char ed = '\n')
{
	static short st[30], idx;
	do st[++ idx] = x % 10, x /= 10; while(x);
	while(idx) putchar(st[idx --] | '0');
	putchar(ed);
}

int main()
{
	int a,b;
	input(a);
	input(b);
	cout << a << endl << b << endl;
	output(a + b); 
	return 0;
}
```
## 终版
```c++
#include<iostream>
#include<cstdio>

#ifdef __LINUX__
#define getchar() getchar_unlocked()
#define putchar(a) putchar_unlocked(a)
#endif

#ifdef __WINDOWS__
#define getchar() _getchar_nolock()
#define putchar(a) _putchar_nolock()
#endif

using namespace std;
 
template <typename T> void input(T &x)
{
    x = 0; char c = getchar(); bool f = false;
    while (c < '0' or c > '9')
    	f |= (c == '-'), c = getchar();
    while  (c >= '0' and c <= '9')
    	x = (x << 3) + (x << 1) + (c ^ '0'), c = getchar();
    x = f ? -x : x;
}

template <typename T, typename... Args>
void input(T &x, Args&... args)
{
    input(x), input(args...);
}

template <typename T>
void output(T x, char ed = ' ')
{
	if (x < 0) putchar('-'), x = -x;
    static short st[64], top;
    top = 0;
    do st[top ++] = x % 10, x /= 10; while (x);
    while (top --) putchar(st[top] ^ '0');
    putchar(ed);
}
template <typename T, typename ...Args>
void output(T x, char ch = ' ', Args... args) {
    output(x, ch);
    output(args...);
}

int main()
{
    int a, b, c;
    input(a, b, c);
    output(a, ' ', b, ' ', c, ' ');
    return 0;
}
```
