---
title: 最小生成树 | OI笔记
date: 2023-1-20 21:16:14
categories: 信息竞赛
tags: 图论
author: Shawn  GrainRain
cover: https://t7.baidu.com/it/u=3734967019,941734598&fm=193&f=GIF
---


# 最小生成树 $MST$

最小生成树指在无向图中找出任意两点都相互连通的树结构的最小边权

值得注意的是，一张无向图的最小生成树可能有很多棵，而如果一张图无法获得一个最小生成树，则得到的是最小生成森林（很形象化的名字）

## $Prim$

### 朴素版 $O(n^2)$

- 适用于稠密图（时间复杂度与边的个数无关）

**算法思想：**

类似于 $Dijkstra$ 算法，先定义一个集合存储最小生成树，之后进行以下几步：

1. 将所有距离初始化为正无穷
2. 进行 $n$ 次迭代，每次迭代中找到集合外**距离集合**最近的点 $t$ 
3. 用 $t$ 更新其他点到**集合**的距离

```cpp
const int N = 510;
const int INF = 0x3f3f3f3f;

int g[N][N];
int dist[N];
bool st[N];

int n ,m;

int prime()
{
	memset(dist, 0x3f, sizeof dist);
	int ans = 0;
	// 用于存储最小生成树中所有边权之和 
	for (int i = 0; i < n; i ++)
	{
		int t = -1;
		for (int j = 1; j <= n; j ++)
			if (!st[j] && (t == -1 || dist[t] > dist[j]))
				t = j;
		// 取出集合外距离集合最近的点 
		if (i && dist[t] == INF) return INF;
		// 如果不是第一个点并且距离为正无穷，说明该图不连通
		// 则没有最小生成树，判无解
		if (i) ans += dist[t];
		// 如果不是第一个点，则将该点边权加入最小生成树 
		
		for (int j = 1; j <= n; j ++)
			dist[j] = min(dist[j], g[t][j]);
		// 使用该点进行更新 
		
		st[t] = true;
		// 将该点加入集合 
	}
	return ans;
}

int main()
{
	cin >> n >> m;
	memset(g, 0x3f, sizeof g);
	while (m --)
	{
		int u, v, w;
		cin >> u >> v >> w;
		g[u][v] = g[v][u] = min(g[v][u], w);
	}
	
	int t = prime();
	
	if (t == INF) puts("impossible");
	else cout << t << endl;
}
```

在 $n$ 次的迭代中循环查找，因此时间复杂度为 $O(n^2)$，类比于 $Dijkstra$ 的二叉堆优化，使用 `priority_queue` 可以将循环中查找的时间复杂度降到 $O(m·log_2n)$，相比之下，$Kruskal$ 在时间复杂度相同的情况下思路更简单，因此更多地使用 $Kruskal$ 

## $Kruskal$ $O(m·log_2m)$

- 适用于稀疏图（时间复杂度随边数变化而变化）

**算法思路：**

1. 将所有边按权重送小到大排序 $O(m·log_2m)$（常数很小）
2. 枚举每条边，设这条边从 $a$ 指向 $b$ ，权重为 $c$。如果当前 $a$ 和 $b$ 所在的集合不连通（使用并查集维护），就将这条边加入集合中

```cpp
const int N = 2e5 + 10;

struct node
{
	int a, b, w;
	
	bool operator < (const node &t) const
	{
		return w < t.w;
	}
}edge[N];

int p[N]; // 并查集 father 数组
int find(int x)
{
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int n, m;

void kruskal()
{
	sort(edge, edge + m);
	for (int i = 1; i <= n; i ++) p[i] = i;
	// 初始化并查集 
	
	int res = 0, cnt = 0;
	// res 存储最小生成树中边权之和
	// cnt 存储最小生成树中边数 
	for (int i = 0; i < m; i ++)
	{
		int a = edge[i].a, b = edge[i].b, w = edge[i].w;

		a = find(a), b = find(b);
		if (a != b)
		{
			p[a] = b; 
			res += w;
			cnt ++;
		}
	}
	if (cnt < n - 1) puts("impossible");
	else cout << res << endl;
}

int main()
{
	cin >> n >> m;
	for (int i = 0; i < m ;i ++)
	{
		int a, b, w;
		cin >> a >> b >> w;
		edge[i] = {a, b, w};
	}
	kruskal();
}
```

# 二分图

二分图是指一个可以将所有点分成两侧，所有的边都是从一侧连向另一侧，内部没有边

当且仅当图中不含奇数环[^1]时，一个图是二分图



## 染色法($dfs$) - 判别二分图

- 时间复杂度由 $dfs$ 继承而来，$O(m + n)$
- 当且仅当一个图可以被二分图染色，这个图是二分图。由于图中不含有奇数环，染色过程中一定不出现矛盾

```cpp
const int N = 1e5 + 10, M = N << 1;

int n, m;
int color[N];

bool dfs(int u, int c)
// point and color
{
	color[u] = c;
	
	for (int i = h[u]; i != -1; i = edge[i].nxt)
	{
		int t = edge[i].to;
		if (!color[t] && !dfs(t, 3 - c)) 
			return false;
		else if (color[t] == c)
			return false;
	}
	return true;
}

int main()
{
	memset(h, -1, sizeof h);
	// init
	cin >> n >> m;
	for (int i = 0; i < m; i ++)
	{
		int a, b;
		cin >> a >> b;
		add(a, b), add(b, a);
	}
	// input
	bool judge = true;
	for (int i = 1; i <= n; i ++)
		if (!color[i] && !dfs(i, 1))
		{
			judge = false;
			break;
		}
	if (judge) puts("Yes");
	else puts("No");
	return 0;
}
```

## 匈牙利算法 - 求二分图的最大匹配

- 最坏情况时间复杂度 $O(mn)$，实际运行效率非常好


```cpp
const int N = 510, M = 1e5 + 10;

int n1, n2, m;

int h[N], idx;
struct node
{
	int val, nxt;
}edge[M];

int match[N];
// 存储右侧的对应关系 
bool st[N];
// 存储是否考虑过该点 

void add(int a, int b)
{
	edge[++ idx].val = b;
	edge[idx].nxt = h[a];
	h[a] = idx;
}

bool find(int x)
{
	for (int i = h[x]; i != -1; i = edge[i].nxt)
	{
		int t = edge[i].val;
		if (!st[t])
		{
			st[t] = true;
			if (match[t] == 0 || find(match[t]))
			// 右侧点可用 
			{
				match[t] = x;
				return true;
			}
		}
	}
	return false;
}

int main()
{
	memset(h, -1, sizeof h);
	// init
	cin >> n1 >> n2 >> m;
	while (m --)
	{
		int a, b;
		cin >> a >> b;
		add(a, b);
	}
	// input
	int res = 0;
	// 当前匹配数量
	for (int i = 1; i <= n1; i ++)
	{
		memset(st, false, sizeof st);
		if (find(i)) res ++;
	}
	cout << res << endl;
	return 0;
}
```

[^1]:奇数环指环当中边的数量为奇数时，我们将这个环叫做二分图
