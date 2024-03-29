---
title: 莫队 | OI笔记
date: 2023-03-26 21:39:16
categories: 信息竞赛
tags: 算法
author: Shawn
cover: https://img2.baidu.com/it/u=1834542261,81809892&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
---
> 莫队算法本质是暴力思想\
离线算法

# 模板题
[P1972 [SDOI2009] HH的项链](https://www.luogu.com.cn/problem/P1972)

* 洛谷卡莫队，AcWing可过qwq

[AcWing 2492.HH的项链](https://www.acwing.com/problem/content/2494/)

## 本质
分析题意易知，本题要找一段序列出现的不同数字

## 暴力思路
时间复杂度$O(snm)$

每次出现一个数字就及记录在`cnt`数组中，然后寻找`cnt`中的非零数字的量，即为结果

## 暴力优化
时间复杂度$O(nm)$

在每次记录`cnt`的时候，当`cnt[i] = 1`时不计入新的数字，只在`cnt[i] = 0`时递增答案

## 双指针优化
时间复杂度$O(nm)$（最坏情况）
用`i`指向末尾，`j`指向开头，每次利用前一个区间来更改下一个区间，维护`cnt`数组

## 莫队做法
> 莫队算法用于优化暴力算法\
调整区间查询的顺序，实现算法的优化

时间复杂度$O(m\sqrt n)$

### 方法分析
+ 右指针：递增
+ 左指针：
    1. 按照**分块**的编号排序
    2. 右端点的下标

### 时间复杂度分析
+ 右指针：则共有$\sqrt n$块，每一块内部走$n$次，一共走$n\sqrt n$次
+ 左指针：
    1. 块内移动：$\sqrt n$， 总$m\sqrt n$
    2. 块间移动：最坏$2\sqrt n$，总$2n$
对以上两个部分取最大值，算法的总时间复杂度为$O(m\sqrt n)$

### 玄学优化
**分两种情况排序**

1. 奇数块按照右端点从小到大排
2. 偶数块按照右端点从大到小排
* 可以反过来

优化思路：
+ 原方法：

第一段右端点从左到右滚动，然后滚回去，再从左向右滚动第二段

+ 优化后方法：

第一段右端点从左到右滚动，直接从右向左滚动第二段

### 代码
```c++
#include<iostream>
#include<cstring>
#include<algorithm>
#include<cmath>
#include<cstdio>

using namespace std;

const int N = 50010, M = 200010, S = 1000010;

int n, m, len;
int w[N], ans[M];//w存每个位置的数，ans存答案
struct Query//存储所有询问
{
    int id, l, r;//id代表询问的数，l, r分别代表左右端点
}q[M];
int cnt[S];//cnt存储每个数出现的次数


int get(int x)//求每一个位置所处块的编号
{
    return x / len;
}

bool cmp(const Query& a, const Query& b)
{
    int i = get(a.l), j = get(b.l);//获取所在块的编号
    if(i != j)
        return i < j;//不同块则判定块的大小
    //本题目未使用奇偶分别排序
    return a.r < b.r; //块内按照从小到大排序
}


void add(int x, int& res)
{
    if(!cnt[x])
        res ++;
    cnt[x] ++;
}

void del(int x, int& res)
{
    cnt[x] --;
    if(!cnt[x])
        res --;
}

int main()
{
    // ios::sync_with_stdio(false);
    // cin.tie(0);
    // cout.tie(0);
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)
        scanf("%d", &w[i]);

    scanf("%d", &m);
    len = max(1, (int)sqrt((double)n * n / m));//计算最大长度
    for(int i = 1; i <= m; i ++)
    {
        int l, r;
        scanf("%d%d", &l, &r);
        q[i] = {i, l, r};
    }

    sort(q + 1, q + m + 1, cmp);

    for(int k = 1, i = 0, j = 1, res = 0; k <= m; k ++)//i在j的后面一位，表示区间为空
    {
        int id = q[k].id, l = q[k].l, r = q[k].r;
        while(i < r)//右节点比r小，则右移右节点(加入数据)
            add(w[++ i], res);
        while(i > r)//右节点比r大，则左移右节点(删除数据)
            del(w[i --], res);
        while(j < l)//左节点比l小，则右移左节点(删除数据)
            del(w[j ++], res);
        while(j > l)//左节点比l大，则左移左节点(加入数据)
            add(w[-- j], res);
        ans[id] = res;
    }

    for(int i = 1; i <= m; i ++)
        printf("%d\n", ans[i]);
    
    return 0;
}
```

# 修改版
[AcWing 2521.数颜色](https://www.acwing.com/problem/content/2523/)
## 思路
原来是一维记录数组的数\
现在变成二维，增加时间维度，每个时间记录当前的串\
因此，莫队使用三个指针,`i`,`j`,`k`，除`k`外维护方法均不变
## 维护时间维度

**分类讨论发生变化的数的情况**
+ 修改的数在区间外部：此时无需修改
+ 修改的数在区间内部：进行一次删除操作再进行一次加入操作即可
+ 由此可知多维护的一维同样只需要$O(1)$的时间复杂度

## 通过交换方法优化

在时间维度向上遍历时交换`x`和`x'`，再向下遍历时再次互换，可以少存一个变量

## 排序方法

1. `l`所在块的编号
2. `r`所在块的编号
3. `t`时间戳

## 时间复杂度分析

>`a`表示单个块内最大个数，`m`表示块的数量

+ `l`移动的时间复杂度
    1. 块内移动，最大距离是`a`，`m`次操作, **时间复杂度为$O(am)$**
    2. 块间移动，共有`n`个块，**时间复杂度为$O(2n)$**
    * 综上，`l`移动**时间复杂度为$O(an)$**
+ `r`移动的时间复杂度
    1. 块内移动：每次都移动`a`步，共有`m`次询问，**时间复杂度为O(am)**
    2. 块间移动：`r`从最左块移动到最右块，因此`r`有$\dfrac{n}{a}$块，**时间复杂度为$O\Big(\dfrac{n ^ 2}{a}t\Big)$**
    * 综上，`r`移动的**时间复杂度为$O(an)$**
+ `t`移动的时间复杂度
    1. `l`有$\dfrac{n}{a}$种情况2
    2. `t`的范围是`1`到`t`
    * 总时间**复杂度为$O\Big(\dfrac{n ^ 2}{a ^ 2}t\Big)$**

+ 最终时间复杂度为$\sqrt[3]{nt}$
```c++
#include<iostream>
#include<cmath>
#include<algorithm>
#include<cstring>
#include<cstdio>

using namespace std;

const int N = 10010, S = 1e7 + 10;

int n, m, len, mq, mc;//len记录每个块的长度
int w[N], cnt[S], ans[N];
struct Query
{
    int id, l, r, t;//t是时间戳
}q[N];//存储所有询问
struct Modify
{
    int p, c;//时间戳，将第p个数修改成c
}c[N];

int get(int x)
{
    return x / len;
}

bool cmp(const Query& a, const Query& b)
{
    int al = get(a.l), ar = get(a.r);
    int bl = get(b.l), br = get(b.r);
    if(al != bl)//左端点排序
        return al < bl;
    if(ar != br)//右端点排序
        return ar < br;
    return a.t < b.t;//时间戳排序
}

void add(int x, int& res)
{
    if(!cnt[x])
        res ++;
    cnt[x] ++;
}
void del(int x, int& res)
{
    cnt[x] --;
    if(!cnt[x])
        res --;
}


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> n >> m;
    for(int i = 1; i <= n; i ++)
        cin >> w[i];
    for(int i = 0; i < m; i ++)
    {
        char op;
        int a, b;
        cin >> op >> a >> b;
        if(op == 'Q')
        {
            mq ++;//询问个数
            q[mq] = {mq, a, b, mc};
        }
        else
        {
            c[++ mc] = {a, b};//修改
        }
    }

    len = cbrt((double)n * max(mc, 1)) + 1;//计算块长度
    sort(q + 1, q + mq + 1, cmp);//按cmp对所有询问排序

    for(int i = 0, j = 1, t = 0, k = 1, res = 0; k <= mq; k ++)//遍历每一个询问
    {
        int id = q[k].id, l = q[k].l, r = q[k].r, tm = q[k].t;
        while(i < r)
            add(w[++ i], res);
        while(i > r)
            del(w[i --], res);
        while(j < l)
            del(w[j ++], res);
        while(j > l)
            add(w[-- j], res);
        while(t < tm)//当前时间小于询问的时间
        {
            t ++;
            if(c[t].p >= j && c[t].p <= i)//询问的范围若在区间范围之内
            {
                del(w[c[t].p], res);//删去原数
                add(c[t].c, res);//加上新数
            }
            swap(w[c[t].p], c[t].c);//交换这两个数
        }
        while(t > tm)
        {
            if(c[t].p >= j && c[t].p <= i)
            {
                del(w[c[t].p], res);
                add(c[t].c, res);
            }
            swap(w[c[t].p], c[t].c);
            t --;
        }
        ans[id] = res;
    }
    for(int i = 1; i <= mq; i ++)
        cout << ans[i] << endl;
    
    return 0;
}

```

# 回滚莫队算法

>针对于**插入**简单但是**删除**困难的情况\
暴力相加之后再暴力回滚回去

[例题](https://www.acwing.com/problem/content/2525/)\
求：每个数的**重要度**乘上**出现次数**的最大值

## 排序方法

双关键字排序：\
1. `l`所在块的编号
2. `r`的右端点

## 步骤

1. 暴力求块内的所有区间(询问)，然后所剩的询问都在块间
2. 将跨块的询问分成两部分———在块1的部分和不在块1的部分
    1. 右边：每次直接插入，此时`cnt`和`res`存储的是`j`到结尾的结果
    2. 左边：每次都暴力加入，然后再暴力删掉
* 删除操作中`cnt`容易维护，`res`不容易维护，因此在操作第一部分时存储`res`，在操作之后恢复

```c++
#include<iostream>
#include<cstring>
#include<cstdio>
#include<algorithm>
#include<vector>
#include<cmath>

const int N = 100010;
#define int long long

using namespace std;

int n, m, len;
int w[N], ans[N], cnt[N];

struct Query
{
    int id, l, r;
}q[N];

vector<int> nums;

int get(int x)
{
    return x / len;
}

bool cmp(const Query& a, const Query& b)
{
    int i = get(a.l), j = get(b.l);
    if(i != j)
        return i < j;
    return a.r < b.r;
}

void add(int x, int& res)
{
    cnt[x] ++;
    res = max(res, cnt[x] * nums[x]);//nums[x]表示原来的值
}

signed main()
{
    cin >> n >> m;
    len = sqrt(n);
    for(int i = 1; i <= n; i ++)
    {
        cin >> w[i];
        nums.push_back(w[i]);
    }

    //离散化
    sort(nums.begin(), nums.end());
    nums.erase(unique(nums.begin(), nums.end()), nums.end());
    for(int i = 1; i <= n; i ++)
        w[i] = lower_bound(nums.begin(), nums.end(), w[i]) - nums.begin();
    
    for(int i = 0; i < m; i ++)
    {
        int l, r;
        cin >> l >> r;
        q[i] = {i, l, r};
    }
    sort(q, q + m, cmp);

    for(int x = 0; x < m;)//从第零个块开始，枚举到第m个块
    {
        int y = x;
        while(y < m && get(q[y].l) == get(q[x].l))//y的块号和x的块号相同
            y ++;//操作完成后x和y之间的部分就是当前块里所有的部分
        int right = get(q[x].l) * len + len - 1; //求这个块的尾端点
        
        while(x < y && q[x].r <= right)//当前右端点在块内
        {//暴力求块内的部分
            int res = 0;
            int id = q[x].id, l = q[x].l, r = q[x].r;
            for(int k = l; k <= r; k ++)
                add(w[k], res);
            ans[id] = res;
            for(int k = l; k <= r; k ++)
                cnt[w[k]] --;
            x ++;
        }

        //求块外的询问
        int res = 0;
        int i = right, j = right + 1;
        while(x < y)
        {
            int id = q[x].id, l = q[x].l, r = q[x].r;
            while(i < r)//使右端点到达指定位置
                add(w[++ i], res);
            int backup = res;//备份res
            while(j > l)//加上左边的部分
                add(w[-- j], res);
            ans[id] = res;
            while(j < right + 1)//删除加入的数
                cnt[w[j ++]] --;
            res = backup;
            x ++;
        }
        memset(cnt, 0, sizeof(cnt));
    }

    for(int i = 0; i < m; i ++)
        cout << ans[i] << endl;
    
    return 0;
}



```
