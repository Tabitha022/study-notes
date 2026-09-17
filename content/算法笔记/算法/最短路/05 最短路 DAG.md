**使用类型**
> 有向无环图

```cpp
void solve(){  
    int n, m; cin >> n >> m;
    vector<pii> G[m + 1];
    vi cnt(n + 1);
    rep(i, 1, m){
        int u, v, w; cin >> u >> v >> w;
        G[u].push_back({v, w});
        cnt[v] ++;
    }

    vi dis(n + 1, INF);
    dis[1] = 0;
  
    queue<int> q;
    rep(i, 1, n){
        if(!cnt[i]) q.push(i);
    }

    while(q.size()){
        int u = q.front();
        q.pop();
        for(auto [v, w] : G[u]){
            if(dis[v] > dis[u] + w)
                dis[v] = dis[u] + w;
            cnt[v] --;
            if(!cnt[v])
                q.push(v);
        }
    }
    cout << dis[n] << endl;
    return;
}
```
