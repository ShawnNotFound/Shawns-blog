---
title: 进阶动态规划1.1 - 数字三角形模型 | OI笔记
date: 2023-03-06 22:31:06
categories: 信息竞赛
tags: 学习笔记
author: Shawn
cover: https://img1.baidu.com/it/u=3012806272,1276873993&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500
---
# 数字三角形
>从集合角度来考虑$DP$问题\
        <p align="right">——闫氏DP分析法</p>
## 原型
[AcWing 898.数字三角形](https://acwing.com/problem/content/898)
详见算法基础课

## 例1
[AcWing 1017.摘花生](https://acwing.com/problem/content/1017)
### 分析
思路较为简单，不过多赘述
`f[i][j]`代表走到`(i, j)`点能获得的最大花生数量\
状态转移方程为`max(f[i - 1][j] + map[i][j], f[i][j - 1] + map[i][j])`
### 代码(手打)
``` c++
#include<iostream>
#include<cmath>
#include<algorithm>
#include<cstring>

using namespace std;

int map[110][110], f[110][110];
int t, r, c;

int main()
{
    cin >> t;
    for(int i = 1; i <= t; i ++)
    {
        cin >> r >> c;
        for(int j = 1; j <= r; j ++)
            for(int k = 1; k <= c; k ++)
                cin >> map[j][k];
        //f[i][j]表示走到map[i][j]的最大获得花生数
        for(int j = 1; j <= r; j ++)
            for(int k = 1; k <= c; k ++)
            {
                f[j][k] += max(f[j - 1][k] + map[j][k], f[j][k - 1] + map[j][k]);
            }
        cout << f[r][c] << endl;
        memset(f, 0, sizeof(f));
        memset(map, 0, sizeof(map));
    }
    return 0;
}
```
## 例2
[AcWing 1018.最低通行费](https://www.acwing.com/problem/content/1020/)
>这道题给我的启示：
> <center><strong>做题之前一定要动脑<strong></center>

### 先看个错误案例
我的代码：
```c++
#include<iostream>
#include<cmath>
#include<algorithm>
#include<cstring>
const int N = 1e2 + 10;
using namespace std;

struct road
{
    int t;
    int c;
}f[N][N];

int map[N][N];

int main()
{
    int n;
    cin >> n;
    int t = 2 * n - 1;
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= n; j ++)
            cin >> map[i][j];
    
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= n; j ++)
        {
            if(f[i - 1][j].c + map[i][j] > f[i][j - 1].c + map[i][j])
            {
                f[i][j].c = f[i][j - 1].c + map[i][j];
                f[i][j].t = f[i][j - 1].t + 1;
                if(f[i][j].t == t && i != n && j != n)
                {
                    f[i][j].c = f[i - 1][j].c + map[i][j];
                    f[i][j].t = f[i - 1][j].t + 1;
                        if(f[i][j].t == t && i != n && j != n)
                            f[i][j].c = 0x3f;
                } 
            }
            if(f[i - 1][j].c + map[i][j] < f[i][j - 1].c + map[i][j])
            {
                f[i][j].c = f[i - 1][j].c + map[i][j];
                f[i][j].t = f[i - 1][j].t + 1;
                if(f[i][j].t == t && i != n && j != n)
                {
                    f[i][j].c = f[i][j - 1].c + map[i][j];
                    f[i][j].t = f[i][j - 1].t + 1;
                        if(f[i][j].t == t && i != n && j != n)
                            f[i][j].c = 0x3f;
                } 
            }
            if(f[i - 1][j].c == f[i][j - 1].c)
            {
                f[i][j].c = f[i - 1][j].c;
                f[i][j].t = min(f[i - 1][j].t, f[i][j - 1].t) + 1;
                if(f[i][j].t == t && i != n && j != n)
                    f[i][j].c = 0x3f;
            }
        }
    
    cout << f[n][n].c;

    return 0;
}
```
题解代码
```c++
#include <iostream>
#include <cstring>

using namespace std;

const int N = 110;

int n;
int w[N][N];
int f[N][N];

int main()
{
    //input
    cin >> n;
    for (int i = 1; i <= n; ++ i)
    {
        for (int j = 1; j <= n; ++ j)
        {
            cin >> w[i][j];
        }
    }

    //initialize
    memset(f, 0x3f, sizeof f);
    f[1][1] = w[1][1];

    //dp
    for (int i = 1; i <= n; ++ i)
    {
        for (int j = 1; j <= n; ++ j)
        {
            f[i][j] = min(f[i][j], f[i - 1][j] + w[i][j]);
            f[i][j] = min(f[i][j], f[i][j - 1] + w[i][j]);
        }
    }

    //output
    cout << f[n][n] << endl;

    return 0;
}
```
>很显然我做这道题没有经过思考，但是这种近似暴力的算法其实也是能AC的\
个人认为，这种做法在考场上可以作为一种下下策，但是肯定会像今年T1一样重写一遍的

### 题目分析

这道题其实并不复杂，是一道基础的**线性$DP$题目**\
这道题的切入点就是他的时间要求是`2n - 1`，恰好和边长`n`有一定的关系
时间就是经过的方格数，也就是说要求路径的**曼哈顿距离**是`2n - 1`，我们可以注意到，这个恰好就是**仅仅向右下走不向左上走**的方法所需的曼哈顿距离

因此我们只需要考虑这道题目中“花费”这个变量，很容易求出简洁的状态转移方程，即`f[i][j] = min(f[i - 1][j], f[i][j - 1]) + map[i][j];`

最后就可以是简单的写代码过程啦~

## 例3
[AcWing 1027.方格取数](https://www.acwing.com/problem/content/1027/)
### 题目分析
1. 处理**两个路线**的走法\
使用`f[i1][j1][i2][j2]`表示分别走到`(i1, j1)`，`(i2, j2)`的状态\
并且让两个点同时从`(1, 1)`，`(1, 1)`开始走
2. 处理同一个格子不能被**重复选择**
只有在`i1 + j1 == i2 + j2`时，两条路径的格子才可能重合
3. **状态优化**
根据(2)，假设`i1 + j1 = i2 + j2 = k`\
则可以使用`f[k][i1][i2]`表示从`(1, 1)`，`(1, 1)`分别走到`(i1, k - i1)`，`(i2, k - i2)`路径的最大值
>之所以使用这种表示方法是为了保证每个格子只能被走一次，因为此时两个点是在一个位置上的，因此可以保证不重复走一个格子

4. 你以为这就完了吗？
>孩子你还是太天真了

这道题有很多细节 ~导致我顺着思路改了一个多小时交了五次才AC~
1. `k`的遍历应该是从`2`开始，因为~`1 + 1 = 2`~起始点是`(1, 1)`，恰好`k`代表的是两者的和，因此当然是从`2`开始啦
2. 千万千万不要忘了判`i1`和`i2`的关系，如果二者相等意味着两者的坐标相等，也就是说此时只能加一次这个格子的数
3. 在遍历两者的时候，加一条判断`j1`和`j2`的语句，因为可能会出现`k - i`越界的情况，所以务必要判断！！！

### 核心代码
```c++
    //省略头文件，定义，读入以及对max的inline重定义，使其能够找出四个数中的最大值
    for(int k = 2; k <= n * 2; k ++)
    {
        for(int i1 = 1; i1 <= n; i1 ++)
        {
            for(int i2 = 1; i2 <= n; i2 ++)
            {
                /*
                1.①向右，②向右 f[k - 1][i1][i2] + map[i1][k - i1] + map[i2][k - i2]
                2.①向下，②向下 f[k - 1][i1 - 1][i2 - 1] + map[i1][k - i1] + map[i2][k - i2]
                */
                if(k - i1 <= 0 || k - i1 > n || k - i2 <= 0 || k - i2 > n) continue;
                if(i1 == i2)
                {
                    f[k][i1][i2] = max(f[k - 1][i1][i2], f[k - 1][i1 - 1][i2], f[k - 1][i1][i2 - 1], f[k - 1][i1 - 1][i2 - 1]) + map[i1][k - i1];
                    continue;
                }
                
                
                f[k][i1][i2] = max(f[k - 1][i1][i2], f[k - 1][i1 - 1][i2], f[k - 1][i1][i2 - 1], f[k - 1][i1 - 1][i2 - 1]) + map[i1][k - i1] + map[i2][k - i2];
            }
        }
    }
```
## 例4
[AcWing 275.传纸条](https://www.acwing.com/problem/content/275/)

>这道题不能说跟例3相似，只能说完全一样

按照例三思路分析，只需要将`k`的循环中的`n * 2`改成`x + y`即可

### 直接上代码了

``` c++
#include<iostream>
#include<cmath>
#include<algorithm>
#include<cstring>

const int N = 55;

using namespace std;

int x, y;
int f[N * 2][N][N], map[N][N];

inline short max(int a, int b, int c, int d)
{
    int tmp = max(a, b);
    tmp = max(tmp, c);
    return max(tmp, d);
}

int main()
{
    cin >> x >> y;
    for(int i = 1; i <= x; i ++)
        for(int j = 1; j <= y; j ++)
            cin >> map[i][j];

    for(int k = 1; k <= x + y; k ++)
        for(int i1 = 1; i1 <= x; i1 ++)
            for(int i2 = 1; i2 <= x; i2 ++)
            {
                if(k - i1 <= 0 || k - i1 > y || k - i2 <= 0 || k - i2 > y) continue;
                if(i1 == i2)
                {
                    f[k][i1][i2] = max(f[k - 1][i1][i2], f[k - 1][i1 - 1][i2], f[k - 1][i1][i2 - 1], f[k - 1][i1 - 1][i2 - 1]) + map[i1][k - i1];
                    continue;
                }
                f[k][i1][i2] = max(f[k - 1][i1][i2], f[k - 1][i1 - 1][i2], f[k - 1][i1][i2 - 1], f[k - 1][i1 - 1][i2 - 1]) + map[i1][k - i1] + map[i2][k - i2];
            }

    cout << f[x + y][x][x];

    return 0;
}
```
