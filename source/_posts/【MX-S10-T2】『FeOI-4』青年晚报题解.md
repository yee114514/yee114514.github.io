---
title: 【MX-S10-T2】『FeOI-4』青年晚报题解
date: 2025-11-10
tags:题解
---

题目链接：[洛谷P14461](https://www.luogu.com.cn/problem/P14461)

去图书馆打的比赛，结果到了图书馆发现忘带草稿本和笔了，遇到推式子题只好跳过，不然就场切了/ll  
我们先定义 $F^a$ 表示对 $F$ 进行了 $a$ 次 $()^{'}$ 操作，然后推式子可以得到下式：
$$
F = \sum_{i=0}^mf_ix^i\\
F^{'} = F^1 = \sum_{i=0}^m(i+1)f_{i+1}x^i = \sum_{i=0}^{m-1}(i+1)f_{i+1}x^i \\
F^{''} = F^2 = \sum_{i=0}^m(i+1)(i+2)f_{i+2}x^i = \sum_{i=0}^{m-2}(i+1)(i+2)f_{i+2}x^i \\
............\\
\Downarrow \\
F^a=\sum_{i=0}^{m-a}A_{i+a}^af_{i+a}x^i
$$
易证
$$
(F+G)^a = F^a+G^a
$$
所以
$$
\begin{aligned}
F_n &= G_{n-1}+G_{n-1}^{'} \\
    &= (F_{n-2}-F_{n-2}^{'})+(F_{n-2}-F_{n-2}^{'})^{'} \\
    &= F_{n-2}-F_{n-2}^{'}+F_{n-2}^{'}-F_{n-2}^{''} \\
    &= F_{n-2}-F_{n-2}^{''} \\
    &= F_{n-4}-2F_{n-4}^{''}+F_{n-4}^{''''} \\
    &= F_{n-4}-2F_{n-4}^2+F_{n-4}^4 \\
    &= F_{n-6}-3F_{n-6}^2+3F_{n-6}^4-F_{n-6}^6
\end{aligned}
$$
到这一步，不难发现，$F$ 前的系数便是杨辉三角数。  
所以整理式子可得
$$
F_n=\sum_{i=0}^{\frac{n}{2}}(-1)^i\binom{\frac{n}{2}}{i}F_0^{2i}
$$
同理可得
$$
G_n=\sum_{i=0}^{\frac{n}{2}}(-1)^i\binom{\frac{n}{2}}{i}G_0^{2i}
$$
显然，当 $n>m$ 时，$F_0^n=0$，所以我们只需要遍历到 $2i>m$ 时即可退出循环。  
当 $n$ 为奇数时，只需要先算一步，就可以将 $n$ 变为偶数。  
需要注意的是，$n$ 非常大，但我们只关心 $\binom{n}{k}$ 的值，所以可以预处理组合数。  
时间复杂度 $O(m^2)$ 。  
```cpp
#include <bits/stdc++.h>

#define int long long

using namespace std;

const int mod = 1e9 + 7;
const int MAXN = 5e3 + 5;

int qp(int a, int b)
{
    int base = a % mod, res = 1;
    while (b)
    {
        if (b & 1)
            (res *= base) %= mod;
        (base *= base) %= mod;
        b >>= 1;
    }
    return res;
}

int C[MAXN];
int F[MAXN], G[MAXN];
int fac[MAXN], inv[MAXN];
int f[MAXN][MAXN], g[MAXN][MAXN];

int A(int n, int m)
{
    return fac[n] * inv[n - m] % mod;
}

signed main()
{
    fac[0] = inv[0] = 1;
    for (int i = 1; i < MAXN; i++)
        fac[i] = i * fac[i - 1] % mod;
    inv[MAXN - 1] = qp(fac[MAXN - 1], mod - 2);
    for (int i = MAXN - 2; i; i--)
        inv[i] = inv[i + 1] * (i + 1) % mod;
    int n, m;
    cin >> n >> m;
    for (int i = 0; i <= m; i++)
        cin >> f[0][i];
    for (int i = 0; i <= m; i++)
        cin >> g[0][i];
    if (n & 1)
    {
        swap(f[0], g[0]);
        for (int i = 0; i <= m; i++)
            (f[0][i] += (i + 1) * f[0][i + 1] % mod) %= mod,
                (g[0][i] -= (i + 1) * g[0][i + 1] % mod) %= mod;
        n--;
    }
    for (int j = 2; j <= min(n, m); j += 2)
        for (int i = 0; i <= m - j; i++)
            f[j / 2][i] = A(i + j, j) * f[0][i + j] % mod,
                  g[j / 2][i] = A(i + j, j) * g[0][i + j] % mod;
    n /= 2;
    for (int t = 0; t <= m; t++)
    {
        int res = 1;
        for (int i = n; i >= n - t + 1; i--)
            (res *= i) %= mod;
        C[t] = res * inv[t] % mod;
    }

    for (int i = 0; i <= n; i++)
    {
        for (int j = 0; j <= m; j++)
            if (i & 1)
                (F[j] -= C[i] * f[i][j] % mod) %= mod,
                    (G[j] -= C[i] * g[i][j] % mod) %= mod;
            else
                (F[j] += C[i] * f[i][j] % mod) %= mod,
                    (G[j] += C[i] * g[i][j] % mod) %= mod;
        if (i > m)
            break;
    }
    for (int i = 0; i <= m; i++)
        cout << (F[i] + mod) % mod << " \n"[i == m];
    for (int i = 0; i <= m; i++)
        cout << (G[i] + mod) % mod << " \n"[i == m];
    return 0;
}
```
