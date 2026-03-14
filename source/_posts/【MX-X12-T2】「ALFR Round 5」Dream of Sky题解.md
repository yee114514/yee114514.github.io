---
title: 【MX-X12-T2】「ALFR Round 5」Dream of Sky
date: 2025-05-06
tags: 题解
---

题目链接：[洛谷P12419](https://www.luogu.com.cn/problem/P12419)



首先，不难发现，当 $len(r)-len(l)>=1$ 时（ $len(n)$ 指 $\left \lceil log_2n \right \rceil $ ，即 $n$ 在二进制下的 $01$ 字符串长度 ），必定存在一个数 $2^l-1$ 满足条件，且二进制下全为 $1$ ，故此时 $k=1$ 。

那么考虑 $len(l)==len(r)$ ，设 $a_{i,j}(j \in \{0,1\})$ 表示权值最小的二进制数 $n$ 的第 $i$ 位在最保守的情况下是否可以取 $j$ 。 $a$ 数组的求法很简单：如果 $l_i$ 为 $0$ ，那么 $n_i$ 必定可以取 $0$ ；如果 $r_i$ 为 $1$ ，那么 $n_i$ 必定可以取 $1$ ，即
$$
\begin{cases}
l_i=0 \  \Rightarrow \  a_{i,0}=True\\
r_i=1 \  \Rightarrow \  a_{i,1}=True
\end{cases}
$$
至此，问题便转化为：起点为 $a_{0,1}$，初始代价为 $1$，需要找到一条从 $a_{0,0}$ 到 $a_{n,0}$ 或 $a_{n,1}$ 的路径，路径上只能有 $1$，每次前进只能进行两种操作之一：前进一格（即 $i+=1$ ）或向斜前方进一格（即 $i+=1,j=j\otimes1 $），第二种操作需要付出 $1$ 的代价，求最小代价。显然，我们可以进行动态规划：设 $f_{i,j}$ 为走到第 $i,j$ 格时，所付出的最小代价，则很容易推出转移方程
$$
\begin{cases}
f_{i,0}=min\{f_{i,0}\ ,\ f_{i-1,0}\ ,\ f_{i-1,1}+1\},\ a_{i,0}=True\\
f_{i,1}=min\{f_{i,0}\ ,\ f_{i-1,1}\ ,\ f_{i-1,0}+1\},\ a_{i,1}=True\\
\end{cases}
$$
这就结束了吗？事实上，是有可能出现 $a_{i,0}=a_{i,1}=False$ 的情况的，但是，不难证明，如果 $a_{i,0}=a_{i,1}=False$，则必然存在 $j<i$，使得 $a_{j,0}=a_{j,1}=True$，而这也就说明，$l_j=0$ 且 $r_j=1$，此时，如果在 $n_j$ 处取 $0$，则后面的数可以全部取 $1$，即 $f_{n,1}=f_{j,0}+1$；如果在 $n_j$ 处取 $1$，则后面的数可以全部取 $0$，即 $f_{n,1}=f_{j,1}+1$。那么，完整的状态转移方程应该如下
$$
\begin{cases}
f_{i,0}=min\{f_{i,0}\ ,\ f_{i-1,0}\ ,\ f_{i-1,1}+1\},\ a_{i,0}=True\\
f_{i,1}=min\{f_{i,0}\ ,\ f_{i-1,1}\ ,\ f_{i-1,0}+1\},\ a_{i,1}=True\\
f_{n,1}=min\{f_{n,1}\ ,\ f_{j,0}+1\ ,\ f_{j,1}+1\},\ a_{i,0}=True \cap a_{i,1}=True
\end{cases}
$$
当出现 $a_{i,0}=a_{i,1}=False$ 的情况时，可以直接 $break$，因为由于此时 $f_{i,0}$ 和 $f_{i,1}$ 都无法被转移，所以后面的转移便没有意义。  
答案即为 $min\{f_{n,0},f_{n,1}\}$ 。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 5e5 + 5;

bool a[MAXN][2];
int f[MAXN][2];

int main()
{
    int T;
    cin >> T;
    while (T--)
    {
        memset(a, 0, sizeof(a));
        memset(f, 0x3f, sizeof(f));
        string l, r;
        cin >> l >> r;
        if (r.size() - l.size() >= 1)
        {
            cout << "1\n";
            continue;
        }
        int n = l.size() - 1;
        for (int i = 0; i <= n; i++)
        {
            a[i][0] = (l[i] - '0' == 0);
            a[i][1] = (r[i] - '0' == 1);
        }
        f[0][1] = 1;
        for (int i = 1; i <= n; i++)
        {
            if (!a[i][0] && !a[i][1])
                break;
            if (a[i][0])
                f[i][0] = min({f[i][0], f[i - 1][0], f[i - 1][1] + 1});
            if (a[i][1])
                f[i][1] = min({f[i][1], f[i - 1][1], f[i - 1][0] + 1});
            if (a[i][0] && a[i][1])
                f[n][1] = min({f[n][1], f[i][0] + 1, f[i][1] + 1});
        }
        cout << min(f[n][0], f[n][1]) << "\n";
    }
    return 0;
}
```
