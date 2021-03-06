# 洛谷 P1220 关路灯

区间dp

可以用$d(i, j), i<j$表示关掉第i至j盏灯的最小功耗，显然，在该问题中，老张最后关闭的必然是第i或第j盏灯，故$d(i, j)$可由$d(i+1, j)$和$d(i, j-1)$加上移动的损耗转移而来。

然而，以$d(i-1, j)$至$d(i, j)$为例，我们无法得知在$d(i-1, j)$时老张所处的位置是左端点还是右端点，而不同位置转移至$d(i, j)$的损耗是不同的（因为行走的时间不同）。

所以，我们可以在状态中加上一项k，即$d(i, j, k)$，当k == 0时，表示位于左端点（i），当k == 1时，表示位于右端点（j）。

同时，我们还需定义损失函数cost，由于位移过程中的损耗与当前开启的路灯、行走的时间有关，故定义损失函数$cost(i, j, p, a)$，表示第i至j盏灯关闭的条件下，从第p盏灯走到第a盏灯的损耗。

至此，我们可以列出状态转移方程：

```c++
int v1 = dp[i+1][j][0] + get_cost(i+1, j, i+1, i);
int v2 = dp[i+1][j][1] + get_cost(i+1, j, j, i);
dp[i][j][0] = min(v1, v2);
int v3 = dp[i][j-1][1] + get_cost(i, j-1, j-1, j);
int v4 = dp[i][j-1][0] + get_cost(i, j-1, i, j);
dp[i][j][1] = min(v3, v4);
```

值得注意的是，根据题目所给数据，我们可以直接得到$d(c, j, 0/1), c<j\le n$和$d(i, c, 0/1),0<i<c$，即只开一边灯的情况。为了不在迭代顺序上折腾，我选择直接在初始化中进行处理。

```c++
for(int i = c+1; i <= n; ++i)
{
    dp[c][i][1] = dp[c][i-1][1] + get_cost(c, i-1, i-1, i);
    dp[c][i][0] = dp[c][i][1] + get_cost(c, i, i, c);
}
for(int i = c-1; i > 0; --i)
{
    dp[i][c][0] = dp[i+1][c][0] + get_cost(i+1, c, i+1, i);
    dp[i][c][1] = dp[i][c][0] + get_cost(i, c, i, c);
}    
```



此外，损失的计算需要为开启的路灯的功率和，可以在读入过程中计算前缀和优化

```c++
inline int get_cost(int i, int j, int p, int aim)
{
    return (sum[i-1]+sum[n]-sum[j]) * abs(l[p].pos - l[aim].pos);
    //l[i].pos表示第i盏灯的位置
}
```



进行完以上两项处理之后，dp过程就相对简单了很多。但需要注意，$d(i, j, k)$的前置状态为$d(i+1, j, k)$和$d(i, j-1, k)$，也即是说，计算前者时必须将后者计算出，故在递推时，应以c为中心，i递减，j递增

```c++
    for(int i = c-1; i > 0; --i)
        for(int j = c+1; j <= n; ++j)
        	//dp
```



以上就是该题需要注意的地方。