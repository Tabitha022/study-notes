> 写状压的时候别想其他事情，容易写错！

> $\large vector$ 交换 ： $\large a.swap(b)$ 为 $\large a$ 和 $\large b$ 的内容交换
## [位运算]

```cpp
|  只要有 1 ，就是 1
~  取反
&  都是 1 的时候才是 1
^  不同时才是 1
```

***

# ==棋盘式 - 网格连通性== 

> 通常为给出一个 $\large n \times m$ 大小的棋盘，我们使用 `1` 和 `0` 表示格子占用情况，使用二进制的整数表示一行的格子占用情况 ，最后动态向下面一行转移

## [P1896 [SCOI2005] 互不侵犯](https://www.luogu.com.cn/problem/P1896)

**题意**
在 $\large N \times N$ 的棋盘上，摆放 $\large K$ 个国王，保证互相之间不会攻击，有多少种方案.
注意： 一个国王会攻击周围八个点的国王

$\large 1 \le N \le 9$ , $\large  1 \le K \le N \times N$

**题解**
使用状压，记录每一行的摆放方式 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl "\n"
#define int long long
#define rep(i, a, b) for(int i = a; i <= b; i ++)
#define per(i, a, b) for(int i = a; i >= b; i --)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc (p << 1)
//#define rc ((p << 1) + 1)
const int INF = 1e18;
const int N = 5e6 + 10;  
//const int mod = 1e9 + 7;
//const int offset = N;

void solve(){
    int n, k; cin >> n >> k;
    vi type;
    vi cnt;
    int t_max = 2;
    rep(i, 1, n - 1){
        t_max *= 2;
    }
    t_max -= 1; //最多摆放的情况
    //cout << t_max << endl;
    rep(i, 0, t_max){
        if((((i >> 1) | (i << 1)) & i) != 0) continue; //遍历每一种摆放情况，可能存在本身行之间就矛盾的情况
        int num = 0; 
        int x = i;
        while(x){
            int tmp = x % 2;
            x /= 2;
            if(tmp){
                num ++; //这是记录每个情况下的 1 的个数， 其实也可以使用 >> j & 1?
            }

        }
        //if(i == 2) cout << "no" << endl;
        if(num > k) continue;
        //cout << i << endl;
        type.push_back(i);
        cnt.push_back(num);
    }

    int m = type.size() - 1;
    viii dp(n + 1, vii(k + 1, vi(m + 1)));
    dp[0][0][0] = 1;
    rep(i, 1, n){
        rep(j, 0, m){
            int c = cnt[j]; 
            int s = type[j];

            rep(l, 0, m){
                int c2 = cnt[l]; //这次要摆多少个
                int s2 = type[l]; 
                rep(p, 0, k - c){
                    if(p < c2) continue;
                    if(((s >> 1 | s | s << 1) & s2) != 0) continue;
                    dp[i][p][j] += dp[i - 1][p - c2][l];
                }
            }
        }
    }

    int res = 0;

    rep(i, 0, m){
        if(k - cnt[i] < 0) continue;
        res += dp[n][k - cnt[i]][i];
        //cout << dp[n][k - cnt[i]][i] << endl;
    }
    cout << res << endl;
    return;
}
signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

# ==集合==

> 集合类型的状压 $\large dp$ 通常表现为一个元素的选或不选，使用二进制整数表示一个集合内元素的选择情况
> 注： 分为可重复选和不可重复选两种情况

## [最短 Hamilton 路径]
**题意**
给定一张 $\large N$ 个点的带权无向图，点从 $\large 0 \sim n - 1$ 标号，求起点 $\large 0$ 到终点 $\large n - 1$ 的最短 $\large Hamilton$ 路径
$\large Hamilton$ 路径的定义是 $\large 0$ 到 $\large n - 1$ 不重不漏地经过每个点恰好一次

保证直接走到比绕路更短
$\large 1 \le n \le 20$
**题解**
从走过一个点遍历到走过 $\large n - 1$ 个点
需要注意的是必须从 `1` 出发，必须最终走到 $\large n$ 号点
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl "\n"
#define int long long
#define rep(i, a, b) for(int i = a; i <= b; i ++)
#define per(i, a, b) for(int i = a; i >= b; i --)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc (p << 1)
//#define rc ((p << 1) + 1)
const int INF = 1e18;
const int N = 5e6 + 10;  
const int mod = 1e8;
//const int offset = N;

//int n, m;
void solve(){
    int n; cin >> n;
    vii a(n + 1, vi(n + 1));
    vi b(n + 1);
    b[1] = 1;
    rep(i, 2, n){
        b[i] = b[i - 1] * 2;
    }
    rep(i, 1, n)
        rep(j, 1, n) cin >> a[i][j];

    vi type;
    int t_max = 1;
    rep(i, 1, n){
        t_max *= 2;
    }
    //cout << t_max << endl;
    t_max -= 1;
    vi G[n + 1];
    rep(i, 0, t_max){
        if((i & 1) == 0) continue;
        //cout << i << endl;
        int x = i;
        int cnt = 0;
        while(x){
            if(x % 2) cnt ++;
            x /= 2;
        }
        G[cnt].push_back(i);
        type.push_back(i);
    }

    vii dp(n + 1, vi(t_max + 1, INF));
    
    int sum = INF;
    dp[1][1] = 0; //到 1 的付出为 0 ，从 1 开始出发
    //从 cnt 开始走吧？
    rep(i, 1, n - 1){
        //cout << endl << endl << i << endl;
        for(auto v : G[i]){
            rep(j, 1, n - 1){
                if((b[j] & v) == 0) continue;
                rep(k, 1, n){
                    if(i != n - 1 && k == n) continue;
                    if((b[k] & v) != 0) continue;
                    //cout << j << " " << v << endl;
                    dp[k][v + b[k]] = min(dp[k][v + b[k]], dp[j][v] + a[j][k]);
                    //cout << k << " " << v + b[k] << endl;
                    //cout << dp[k][v + b[k]] << endl;;
                }
            }
        }
    }
    cout << dp[n][t_max] << endl;
}


signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T --) solve();
    return 0;
}
```

## [售货员的难题](（https://www.luogu.com.cn/problem/P1171）)

非常典型的通过二进制表示选与不选

## [徒競走](https://atcoder.jp/contests/abc041/tasks/abc041_d)

**题意**
一个有向无环图，有 $\large N$ 个点， $\large M$ 条边，计算拓扑排序种类个数
$\large 1 \le N \le 16$

**题解**
整个二维的涂色板块可以想象成一个有向图（也就是拓扑排序），在一个板块左上方的涂完了之后，这个板块才能涂色，和上一个 **售货员的难题** 这道题目不同点就在于这里

通过集合表示已经出圈的点，遍历集合，遍历下一个出圈的点，需要注意此时这个点还没有在集合中表示出圈，但是它的前驱节点必须在集合中表示出圈

可以通过预先处理出圈个数，进行优化

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long //忘记删unsigned毁了我
#define endl "\n"
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define per(i, a, b) for(int i = a; i >= b; i--)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc p<<1
//#define rc (p<<1) + 1
const int N = 2e6 + 10;
const int M = 5e5 + 10;
const int INF = 1e18;
int mod = 998244353;

void solve(){
    int n, m; cin >> n >> m;
    //将每个点的前驱节点作为一个集合，所以必须要这些点全都是 1 了，这个点才能被触发，所以
    vi G[n + 1];
    rep(i, 1, m){
        int x, y; cin >> x >> y;
        G[y - 1].push_back(x - 1); //和二进制对齐 
    }
    //根据走了多少点了进行转移？
    
    int t_max = 0;
    rep(i, 0, n - 1){
        t_max += (1 << i);
    }

    vi G1[n + 1];
    rep(i, 0, t_max){
        int cnt = 0; 
        rep(j, 0, n - 1){
            if(((i >> j) & 1) != 0) cnt ++;
        }
        G1[cnt].push_back(i); 
    }

    vii dp(t_max + 1, vi(n + 1));
    rep(i, 0, n - 1){ 
        //cout << i << endl;
        for(auto k : G1[i]){
            rep(j, 0, n - 1){ //j是下一个要的点
                if(((k >> j) & 1) != 0) continue; //说明这种情况这个点是已经走到过了 
                int g = 0;
                for(auto p : G[j]){
                    //cout << j << " " << p << endl;
                    if(((k >> p) & 1) == 0){ //j 的前驱节点还没有全部推出
                        g = 1; break;
                    }
                }
                if(g) continue; 
                if(i == 0){dp[1 << j][j] = 1; continue;}
                rep(q, 0, n - 1){
                    //cout << k << " " << q << endl;
                    //cout << dp[k][q] << endl;
                    if(((k >> q) & 1) != 0) dp[k + (1 << j)][j] += dp[k][q];
                    //cout << dp[k + (1 << j)][j] << endl;
                }
            }
        }
    }
    int res = 0;
    rep(i, 0, n - 1){
        res += dp[t_max][i];
    }
    cout << res << endl;
}   

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin>>T;
    while(T--) solve();
    return 0;
}
```

## [平板涂色](https://www.luogu.com.cn/problem/P1283)
有了上一道拓扑排序之后，这道题就变成了拓展题

**题意**
$\large APM$ 只有一把刷子，每次切换颜色都要重新拿起刷子，现在一个平面图上有 $\large N$ 个板块，每个板块有规定要涂的颜色，要求必须一个板块上方紧靠着的板块涂完色了，这个板块才能涂色

$\large 1 \le N \le 16$

**题解**
题目要求必须要一个板块上方紧靠的板块涂完色了才能涂这个板块，所以先找到每个板块的前驱板块，然后转移方程就可以了

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long //忘记删unsigned毁了我
#define endl "\n"
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define per(i, a, b) for(int i = a; i >= b; i--)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc p<<1
//#define rc (p<<1) + 1
const int N = 2e6 + 10;
const int M = 5e5 + 10;
const int INF = 1e18;
int mod = 998244353;

void solve(){
    //由于为上方的染完了就能染色，所以我只记录上方就好了
    int n; cin >> n;
    vector<pii> a(n + 1);
    vector<pii> b(n + 1);
    vi c(n + 1);
    vi G[n + 1]; //记录前驱节点
    rep(i, 0, n - 1) cin >> a[i].first >> a[i].second >> b[i].first >> b[i].second >> c[i]; 

    rep(i, 0, n - 1){
        rep(j, 0, n - 1){
            if(i == j) continue;
            if(b[j].first == a[i].first && a[j].second < b[i].second && b[j].second > a[i].second){
                G[i].push_back(j); //前驱节点
                //cout << i << "  " << j << endl;
            }
        }
    }

    int t_max = 0;
    rep(i, 0, n - 1) t_max += (1 << i); 
    vii dp(t_max + 1, vi(n + 1, INF));
    //遍历涂色情况，遍历下一步涂色板块，遍历上一步涂色板块，上一步的涂色板块是否有要求？，一定是INF，所以可以忽略 
    rep(i, 0, t_max){
        rep(j, 0, n - 1){
            if((i >> j) & 1) continue;
            int g = 0;
            for(auto p : G[j]){
                if(((i >> p) & 1) == 0){g = 1; break;}
            }
            if(g) continue;
            if(i == 0) dp[1 << j][j] = 1; //第一次不需要方程转移
            rep(q, 0, n - 1){
                if((i >> q) & 1){
                    int tmp = 0;
                    if(c[q] != c[j]) tmp = 1;
                    dp[i + (1 << j)][j] = min(dp[i][q] + tmp, dp[i + (1 << j)][j]);
                }
            }
        }
    }

    int res = INF;
    rep(i, 0, n - 1){
        res = min(res, dp[t_max][i]);
    }
    cout << res << endl;
    return;
}   

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin>>T;
    while(T--) solve();
    return 0;
}
```

## [懒得打乱]()

**题意**
一个从 `1` 到 $\large N$ 的排序序列 $\large A$ ， 根据 $\large A_i' = A_{p_i}$ 重新打乱，计算两个逆序对数量差最大时序列 $\large A$ 的排序类型
答案对 `998244353` 进行取模

$\large 1 \le N \le 22$

**题解**
假设从原序列到打乱后的序列一共改变了 $\large m$ 对的相对顺序，那么想要让差值最大，就要求，这 $\large m$ 对要么全都是逆序对，要么全都不是逆序对

根据对称性，求一种情况就可以，所以可以求非逆序对的时候，那么比它小的点就是它的前驱节点，变成一个前驱集合，按照拓扑排序的做法就可以了

```cpp
#include <bits/stdc++.h>
using namespace std;
//#define int long long 
#define endl "\n"
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define per(i, a, b) for(int i = a; i >= b; i--)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc p<<1
//#define rc (p<<1) + 1
const int N = 2e6 + 10;
const int M = 5e5 + 10;
const int INF = 1e18;
int mod = 998244353;

void solve(){
    int n; cin >> n;
    vi a(n + 1);
    //求在 p 序列中的逆序对，最后排数字的时候，直接算都是非逆序对数量，最后 * 2
    rep(i, 0, n - 1){cin >> a[i]; a[i] -= 1;}
    int cnt = 0;
    vi G[n + 1];
    rep(i, 0, n - 1){
        rep(j, i + 1, n - 1){
            if(a[i] > a[j]){ G[j].push_back(i); cnt ++;}
        }
    }


    int t_max = 0;
    rep(i, 0, n - 1) t_max += (1 << i);
    //cout << t_max << endl;

    vi dp(t_max + 1);
    //预处理个数？

    vi t;
    t.push_back(0);
    rep(i, 1, t_max){
        int g = 0;
        rep(j, 0, n - 1){
            if(((i >> j) & 1) == 0) continue;
            for(auto k : G[j]){
                if(((i >> k) & 1) == 0){
                    g = 1; break;
                }
            }
            if(g) continue;
        }
        if(g == 0) t.push_back(i);
    }


    //遍历已经放置的类型， 遍历下一个要放置的点，遍历上一个放置的点
    rep(w, 0, t.size() - 1){
        int i = t[w];
        rep(j, 0, n - 1){
            if(((i >> j) & 1)) continue; //说明这个点已经放置了
            int g = 0;
            for(auto p : G[j]){
                if(((i >> p) & 1) == 0) {g = 1; break;}
            }
            if(g) continue;
            if(i == 0) dp[1 << j] = 1;
            else{
                dp[i + (1 << j)] += dp[i];
                dp[i + (1 << j)] %= mod;
            }
        }
    }
    int res = 0;
    //cout << res << endl;

    res = (res + dp[t_max]) % mod;
    
    //如果没有逆序对的话，就不能 * 2 了，所以需要记录有没有逆序对
    cout << (cnt == 0? res : res * 2 % mod) << endl;
    return;
    
}   

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin>>T;
    while(T--) solve();
    return 0;
}
```

## [组队](https://ac.nowcoder.com/acm/contest/133885/K)

**题意**
一共 $\large 3 \times N$ 名队员，三个人一个队，每两个人之间都有一个默契值，一个队的得分是三个人两两之间的默契值之和，计算所有队伍得分总和的最大值

$\large 1 \le N \le 8$

**题解**
最大为 `24` 名成员，所有选择使用状压 $\large dp$ , 如果只是普通的状压，时间复杂度会达到 $\large O(n ^ 3 2 ^ n)$
所以考虑优化到 $\large O(n 2 ^ n)$ 之内

> 优化方向：
> 从左往右选队伍，也就是相当于如果一个队伍里面编号最小的作为队长，队长去两个队员，左边第一个 `0` 的作为新的队伍的队长


```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long 
#define endl "\n"
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define per(i, a, b) for(int i = a; i >= b; i--)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc p<<1
//#define rc (p<<1) + 1
const int N = 2e5 + 10;
const int M = 5e5 + 10;
const int INF = 1e18;
int mod = 998244353;

void solve(){
    int n; cin >> n;
    vii a(3 * n, vi(3 * n));
    rep(i, 0, 3 * n - 1){
        rep(j, 0, 3 * n - 1) cin >> a[i][j];
    }

    //使用三个 1 表示一个队伍, 0 - 3 * n - 1
    vi tmp[3 * n + 1]; //记录第一个
    vi f;
    int t_max = 0;
    rep(i, 0, 3 * n - 1) t_max += (1 << i);
    vi dp(t_max + 1, -INF);
    dp[0] = 0;
    rep(i, 0, 3 * n - 3){
        rep(j, i + 1, 3 * n - 2){
            rep(k, j + 1, 3 * n - 1){
                tmp[i].push_back((1 << i) + (1 << j) + (1 << k)); //每次选出 3 个人 
                dp[(1 << i) + (1 << j) + (1 << k)] = (a[i][j] + a[i][k] + a[j][k]); //三个组队的
            } 
        }
    }

    vi G[3 *n + 1];
    //int x = 0;
    rep(i, 0, t_max){
        int cnt = 0;
        rep(j, 0, 23){
            if(((i >> j) & 1)){
                cnt ++;
            }
        }
        if(cnt % 3) continue;
        int g = 0;
        rep(j, 0, cnt / 3 - 1){
            if(((i >> j) & 1) == 0){g = 1; break;}
        }
        if(g == 1) continue;
        G[cnt].push_back(i);
        //x ++;
    }
    //cout << x << endl;
    
    for(int c = 3; c <= 3 * n; c += 3){ 
        for(auto i : G[c]){ 
            rep(j, 0, 3 * n - 1){
                if(((i >> j) & 1)){continue;}
                for(auto k : tmp[j]){
                    if(((k & i) != 0)) continue;
                    dp[k | i] = max(dp[k | i], dp[i] + dp[k]);
                }
                break;
            }
        }
    }
    cout << dp[t_max] << endl;
    
    return;
}   

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin>>T;
    while(T--) solve();
    return 0;
}
```

# 轮廓线 $dp$ (插头 / 折线)

> 适用情况：
> 无法整行或者整列表示转移的棋盘类型

与直接的棋盘式（连通型）不同的是，轮廓线 $\large dp$ 是一个格子一个格子转移，每次的状态是一个线形的

> 例题：
> [蒙德里安的梦想](https://www.acwing.com/problem/content/293/)

这道题有两种解法：
1.棋盘式，使用 `1` 和 `0` 表示这个格子被竖着覆盖的还是横着覆盖的，然后状态转移
2.轮廓线 $\large dp$ 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long 
#define endl "\n"
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define per(i, a, b) for(int i = a; i >= b; i--)
#define vi vector<int>
#define vii vector<vi>
#define viii vector<vii>
#define pii pair<int,int>
//#define lc p<<1
//#define rc (p<<1) + 1
const int N = 2e6 + 10;
const int M = 5e5 + 10;
const int INF = 1e18;
int mod = 998244353;

void solve(){
    int n, m;
    while(cin >> n >> m){
        if(n == 0 && m == 0) break;
        
        //遍历格子，遍历上一个格子的状态，转移，如果合法，就转移
        //状态表示为 m + 1 位
        int t_max = 0;
        rep(i, 0, m) t_max += (1 << i); //全部填完的状态

        vi dp(t_max + 1); //记录上一个的状态
        dp[0] = 1;
        rep(i, 0, n - 1){
            rep(j, 0, m - 1){
                vi ndp(t_max + 1);
                rep(k, 0, t_max){ //上一个方块的状态
                    if((k >> 1) & 1){
                        ndp[k >> 1] += dp[k];
                    }
                    if(j < m - 1 && ((k >> 1) & 1) == 0 &&((k >> 2) & 1) == 0){ //需要保证这个点不是整行的最后一个
                        ndp[(k >> 1) | 3] += dp[k];
                    }

                    if(i < n - 1 && ((k >> 1) & 1) == 0){
                        ndp[(k >> 1) | 1 | (1 << m)] += dp[k];
                    }
                }
                ndp.swap(dp);
            }
        }
        cout << dp[1] << endl;
    }
    return;
}   

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin>>T;
    while(T--) solve();
    return 0;
}
```

