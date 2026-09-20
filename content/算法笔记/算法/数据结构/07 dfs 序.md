>  $\large dfn[i]$ ， $\large i$ 被第几个遍历到
>  $\large sz[i]$ ， $\large i$ 的子树有几个点（包括根自己）
>  $\large tout[i]$  ， $\large i$ 的子树中最后几个被遍历到的点的 $\large dfn$

```cpp
vi dfn(n + 1), sz(n + 1, 1), tout(n + 1);
int idx = 0;

void dfs(int u, int fa){
	dfn[u] = ++idx;
	for(int v : G[u]){
		if(v == fa) continue;
		
		dfs(v, u);
		sz[u] += sz[v];
	}
	tout[u] = idx;
}
```
