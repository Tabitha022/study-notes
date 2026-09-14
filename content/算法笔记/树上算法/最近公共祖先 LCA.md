# 倍增求 LCA

> 时间复杂度 ： $\large O((n + m) logn)$
> $\large dfs$ 求出 $\large ST$ 表， 然后利用 $\large ST$ 表求出 $\large lca$

```cpp
vi d(N);
vii f(N, vi(20)); //f[x][i] 表示 x 向上跳 2 ^ i 步

//建立 ST 表
void dfs(int u, int father){
	d[u] = d[father] + 1; //深度
	f[u][0] = father;
	rep(i, 1, 19)
		f[u][i] = f[f[u][i - 1]][i - 1];
	for(auto v : G[u]){
		if(v == father) continue;
		dfs(v, u);
	}
}

//求  u 和 v 的 lca 
int lca(int u, int v){
	if(d[u] < d[v]) swap(u, v); // u 作为深度更深的那一个
	
	per(i, 19, 0)
		if(d[f[u][i]] >= d[v])
			u = f[u][i];
		
	if(u == v) return v; //深度小的那个本省就是它们的 lca 
	
	per(i, 19, 0){
		if(f[u][i] != f[v][i]){
			u = f[u][i];
			v = f[v][i];
		}
	}
	return f[u][0];
}


```



**例题：**

>  下面的例题是一些题面中并没有明确告诉你去求 $\large LCA$ ，但是实际上运用到了 $\large LCA$ 并且需要你自己去求的题目

[天天爱跑步](https://www.luogu.com.cn/problem/P1600)
**题意**

**思路**

记 $\large a$ 的深度为 $\large d(a)$ ,  $\large a, b$ 的最近公共祖先为 $\large lca(a, b)$ , $\large a$ 出现观察员的时间为 $\large w(a)$

$\large s$ 到 $\large t$ 的最短路一定是 从 $\large s$ 先走到了 $\large lca(s, t)$ , 然后再从 $\large lca(s, t)$  走到 $\large t$ .

对于观察点 $\large x$ 来说，有两种情况这个人贡献了价值， 分别为 $\large s$ 走到 $\large lca(s, t)$ 这段路上， 以及 $\large lca(s, t)$ 走到 $\large t$ 这段路上.

第一种情况：

是否经过角度的条件： $\large lca(s, x) = x$ 且 $\large lca(x, lca(s,t)) = lca(s,t)$ 
是否达到时间要求的条件 ： $\large d(s) - d(x) = w(x)$ 

引入一个 $\large cnt1$ 数组 , $\large cnt[x]$ 表示深度等于 $\large x$ 的数量的，使用 $\large dfs$ 遍历

第二种情况：

是否经过角度的条件： $\large lca(x, t) = x$ 且 $\large lca(x, lca(s, t)) = lca(s, t)$
是否达到时间要求的角度 ： $\large d(s) + d(x) - 2 \times d(lca(s, t)) = w(x)$
 

# Tarjan 求 LCA
