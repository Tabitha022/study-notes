# prim

## 朴素算法

```cpp
vi dis(n + 1, INF);
vii a(n + 1, vi(n + 1));
vi vis(n + 1);

rep(i, 1, n){
	int x = -1;
	rep(j, 1, n)
		if(vis[j] && (x == -1 || dis[j] < dis[x])) x = j;
	
	vis[x] = 1;
	res += dis[x];
	
	rep(j, 1, n)
		dis[j] = min(dis[j], a[j][x]);
}
```

## 优先队列优化

```cpp
vi vis(n + 1), dis(n + 1, INF);
priority_queue<pii> q;
q.push({0, 1})

while(q.size()){
	int u = q.top().second;
	int w = -q.top().first;
	q.pop();
	if(vis[u]) continue;
	vis[u] = 1;
	
	for(auto x : G[u]){
		int v = x.first;
		if(vis[v]) continue;
		int w2 = x.second;
		if(w2 < dis[v]) q.push({-w2, v});
	}
}
```