---
title: 最短路算法 | OI笔记
date: 2023-1-17 15:42:34
categories: 信息竞赛
tags: 图论
author: Shawn  GrainRain
cover: https://t7.baidu.com/it/u=2610975262,3538281461&fm=193&f=GIF
---

# 单源最短路径问题
$Single\ Source\ Shortest\ Path$

源点指起点，单源最短路径问题即为求一个点到另一个点的最短路

## $Dijkstra$ 

- 基于贪心的思想
- 适用于正数边权

使用 $n$ 表示点集，$m$ 表示边集，朴素版 $Dijkstra$ 时间复杂度为 $O(n^2)$ ，二叉堆优化的 $Dijkstra$ 时间复杂度为 $O(m·log_2n)$

由于朴素版 $Dijkstra$ 时间复杂度与边数没有关系，因此**稠密图适合使用朴素版 $Dijkstra$**

### 朴素版 $O(n^2)$ 

$Dijkstra$ 算法的基本思想有以下几步：

1. 初始化距离 $dist$ `dist[1] = 0, dist[i] = INF`
1. 循环 $n$ 次重复找到当前位置能确定的 (且未被标记的)，距源点最近的节点 $x$，放入标记最短距离的点 $s$ 数组，再用该点更新与之最近的点


```cpp
const int N = 510;

int map[N][N];
// 邻接矩阵存图
int n, m;
int dist[N];
// 存储点的距离 
bool st[N]; 
// 存储已经确定最短距离的点 

int dijkstra()
{
	memset(dist, 0x3f, sizeof dist);
	dist[1] = 0;
	
	for (int i = 0; i < n; i ++)
	// 迭代 n 次求最短路 
	{
		int t = -1;
		for (int j = 1; j <= n; j ++)
			if (!st[j] && (t == -1 || dist[t] > dist[j]))
				t = j;
		// 更新可扩展的最短路径 
		st[t] = true;
		// t 已确定最小值，标记为最短
		for (int j = 1; j <= n; j ++)
			dist[j] = min(dist[j], dist[t] + map[t][j]);
		// 使用 t 对新的最短边进行更新
	}

	if (dist[n] == 0x3f3f3f3f) return -1;
		// 特判无解 
		return dist[n]; 
}

int main()
{
	cin >> n >> m;
	memset(map, 0x3f, sizeof map);
	
	while (m --)
	{
		int a, b, c;
		cin >> a >> b >> c;
		map[a][b] = min(map[a][b], c);
		// 处理重边与自环 
	}
	cout << dijkstra() << endl;
	return 0;
}
```

在上述算法中，我们枚举了所有点 $n$ 以及与该点相连的其他点，因此 $Dijkstra$ 未优化时的时间复杂度为 $O(n^2)$

在此基础上，如果使用 $STL$ 二叉堆对 $dis$ 数组进行维护，可以达到 $O(m · log_2n)$ 的时间复杂度

```cpp
typedef pair<int, int> PII;

const int N = 1001000;

int h[N], idx;
struct node{
	int to, nxt, w;
}edge[N];

int n, m;
int dist[N];
bool st[N]; 

priority_queue<PII, vector<PII>, greater<PII> > heap;
// 小根堆，每次取出距离源点最近的点
// 双关键字，节点大小以及节点编号 

void add(int a, int b, int c)
{
	edge[++ idx].to = b;
	edge[idx].w = c;
	edge[idx].nxt = h[a];
	h[a] = idx;
}

void dijkstra()
{
	memset(dist, 0x3f, sizeof dist);
	dist[1] = 0;
	
	heap.push({0, 1});
	while (heap.size())
	{
		PII t = heap.top();
		heap.pop();
		int ver = t.second; // 节点编号
		int distance = t.first; // 距离
		
		if (st[ver]) continue;
		// 当已经确定该点的最短路径时，代表当前队列中存储的元素冗余，直接跳过即可 
		st[ver] = true;
		for (int i = h[ver]; i != -1; i = edge[i].nxt)
		{
			int j = edge[i].to;
			if (dist[j] > distance + edge[i].w)
			{
				dist[j] = distance + edge[i].w;
				heap.push({dist[j], j});
			}
		 } 
	}
}
int main()
{
	cin >> n >> m;
	memset(h, -1, sizeof h);
	while (m --)
	{
		int a, b, c;
		cin >> a >> b >> c;
		add(a, b, c);
	}

	dijkstra();
		
	if (dist[n] == 0x3f3f3f3f) puts("-1");
	else cout << dist[n] << endl;
	return 0;
}
```

## $Bellman-Ford$ 

- 时间复杂度 $O(m·n)$
- 适用于对经过的边数有限制的最短路问题
- 可适用于负权边

**基本过程：**

1. 进行 $n$ 次迭代，迭代 $k$ 次表示经过不超过 $k$ 条边所能到达的点的最短路
2. 遍历所有边 $a$, $b$, $w$ 表示从 $a$ 到 $b$ 存在边权为 $w$ 的边
3. 松弛操作 `dist[b] = min(dist[b], dist[a] + w)`

另外，如果在迭代 $n$ 次之后再次迭代仍可更新，则代表图中存在负环

```cpp
const int N = 510, M = 10010;

struct node{
	int a, b, w;
}edge[M];
// 表示从 a 到 b 有一条边权为 w 的边 

int dist[N], last[N];
int n, m, k;

void bellman_ford()
{
	memset(dist, 0x3f, sizeof dist);
	dist[1] = 0;

	for (int i = 0; i < k; i ++)
	{
		memcpy(last, dist, sizeof dist);
		// 备份 dist 数组，存储上一次迭代结果
		for (int j = 0; j < m; j ++)
		{
			int a = edge[j].a, b = edge[j].b, w = edge[j].w;
			dist[b] = min(dist[b], last[a] + w);
			// 使用上一次迭代结果更新，防止前面的结果影响后面 
		}
	}
}

int main()
{
	cin >> n >> m >> k;
	for (int i = 0; i < m; i ++)
	{
		int a, b, w;
		cin >> a >> b >> w;
		edge[i] = {a, b, w};
	}
	bellman_ford();
	if (dist[n] > 0x3f3f3f3f / 2) puts("impossible");
	else cout << dist[n] << endl;
	return 0;
}
```

## spfa 

**算法思路：**

此时我们再观察 $Bellman\ Ford$ 算法的公式 `dist[b] = min(dist[b], dist[a] + w[i])` ，会发现只有在 `dist[a]` 减小之后，`dist[b]` 才有可能进行更新，因此我们可以将与 `dist[]` 减小点相连的点全部入队，每次进行松弛操作，可以达到减少循环的效果。

**具体实现可以分为以下几步：**

1. 将起点放入队列
2. 当队列不空时，取出队头 $t$
3. 更新 $t$ 的所有出边
4. 如果 $t$ 更新到的点不在队列中，将该点距离更新并加入队列

在使用 $spfa$ 判断负环时，只需要多存储从**源点到该点的边数** `cnt[]`，当 `cnt[x] >= n` 时，表示经过了大于 $n$ 条边，由抽屉原理可得，图中必定存在负环。

**spfa的特点有：**

- 可适用于负数边权
- 不可用于负环
- 平均时间复杂度 $O(m)$，由于常数问题，最坏情况下时间复杂度为 $O(n·m)$，经常被~~毒瘤~~出题人卡常

```cpp
const int N = 1001000;

int h[N], idx;
struct node
{
	int to, nxt, w;
}edge[N];

int n, m;
int dist[N];
bool st[N];

void add(int a, int b, int c)
{
	edge[++ idx].to = b;
	edge[idx].w = c;
	edge[idx].nxt = h[a];
	h[a] = idx;
}

void spfa()
{
	memset(dist, 0x3f, sizeof dist);
	dist[1] = 0;
	
	queue<int> q;
	// 存储所有待更新的点 
	q.push(1);
	st[1] = true;
	//  存储当前点是否在队列中
	while (q.size())
	{
		int t = q.front();
		q.pop();
		st[t] = false;
		
		for (int i = h[t]; i != -1; i = edge[i].nxt)
		{
			int j = edge[i].to;
			if (dist[j] > dist[t] + edge[i].w)
			{
				dist[j] = dist[t] + edge[i].w;
				if (!st[j]) q.push(j), st[j] = true;
			}
		}
	}
}

int main()
{
	cin >> n >> m;
	memset(h, -1, sizeof h);
	while (m --)
	{
		int a, b, c;
		cin >> a >> b >> c;
		add(a, b, c);
	}
	spfa();
	
		
	if (dist[n] == 0x3f3f3f3f) puts("-1");
	else cout << dist[n] << endl;
	
	return 0;
	
}
```

### spfa 判负环

使用 `cnt[]` 数组存储最短路径长度，在 `dist[j] = dist[t] + edge[i].w` 更新时记录路径长度 `cnt[j] = cnt[t] + 1` ，大于等于 $n$ 返回即可

```cpp
typedef pair<int, int> PII;

const int N = 1001000;

int h[N], idx;
struct node
{
	int to, nxt, w;
}edge[N];

int n, m;
int dist[N];
bool st[N]; 

int cnt[N];

void add(int a, int b, int c)
{
	edge[++ idx].to = b;
	edge[idx].w = c;
	edge[idx].nxt = h[a];
	h[a] = idx;
}

bool spfa()
{
	queue<int> q;
	for (int i = 1; i <= n; i++)
	{
		st[i] = true;
		q.push(i);
	}
	while (q.size())
	{
		int t = q.front();
		q.pop();
		
		st[t] = false;
		for (int i = h[t]; i != -1; i = edge[i].nxt)
		{
			int j = edge[i].to;
			if (dist[j] > dist[t] + edge[i].w)
			{
				dist[j] = dist[t] + edge[i].w;
				cnt[j] = cnt[t] + 1;
				if (cnt[j] >= n) return true;
				if (!st[j]) q.push(j), st[j] = true;
			}
		}
	}
	return false;
}

int main()
{
	cin >> n >> m;
	memset(h, -1, sizeof h);
	while (m --)
	{
		int a, b, c;
		cin >> a >> b >> c;
		add(a, b, c);
	}
	if (spfa()) cout << "Yes" << endl;
	else cout << "No" << endl;
	
	return 0;
	
}
```

# 多源汇最短路径问题

## $Floyd$

- 基于动态规划的思想
- 时间复杂度 $O(n^3)$

**算法思路：**

基于动态规划， 三维状态$d_{k, i, j}$ 表示**从点 $i$ 经过 $1$ 到 $k$ 这些中间点到达 $j$ 的最短距离**。使用三层循环枚举中间点，每层循环内尝试更新最短路径，因此 $Floyd$ 算法的时间复杂度是 $O(n^3)$

```cpp
void floyd()
{
for (int k = 1; k <= n; k ++)
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= n; j ++)
            dist[i][j] = min(dist[i][k], dist[k][j]);
}
```

