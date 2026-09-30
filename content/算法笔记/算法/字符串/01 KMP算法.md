>  时间复杂度 $\large O(n + m)$
>  $\large n$ 为 $\large s$ 字符串的长度 ， $\large m$ 为 $\large t$ 字符串的长度

**思路**
>  注意计算模式串中 前 $\large i$ 个字符构成的字符串的最长公共前后缀， 得到 $\large next$ 数组
>  例如我当前匹配到 $\large t[j]$ 位置的时候和 $\large s[i]$ 无法匹配， 可知 $\large next[j] = x$ , 那么我们就可以尝试匹配 $\large s[i]$ 和 $\large t[next[j - 1] + 1]$, 如果还是无法匹配就再次进行相同的尝试

 
**计算 $\large next$ 数组**

>  对于构造
```cpp
int m; string t; cin >> m >> t;
t = " " + t;
vi nxt(m + 2, 0);

nxt[1] = 0;
int j = 0;

rep(i, 2, m) {
	while (j > 0 && t[i] != t[j + 1]) j = nxt[j];
	if (t[i] == t[j + 1]) j++;

	if (i < m && t[i + 1] == t[j + 1]) nxt[i] = nxt[j];
	else nxt[i] = j;
}
rep(i, 1, m) cout << nxt[i] << endl;
```

**记录找到串**
```cpp
vi pre(n + 1);
int r = 1;
rep(l, 1, n){
	while(r != 1 && s[l] != t[r]) r = nxt[r - 1] + 1;
	if(r == 1 && t[r] != s[l]) continue;
	else r ++;
	
	if(r > m){
		pre[r] = 1; //这里往前 m - 1 的子串等于 t
		r = nxt[m] + 1;
	}
}
```