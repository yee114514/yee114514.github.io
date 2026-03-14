---
title: 【MX-S11-T1】战争游戏题解
date: 2025-11-16
tags: 题解
---

题目链接：[洛谷P14520](https://www.luogu.com.cn/problem/P14520)



不难发现答案有单调性，所以答案一定是一串 $0$ 加一串 $1$ 的形式。  
感性思考一下，似乎 $0$ 和 $1$ 的分界点与士兵总数有关，于是猜测第一个使 $sum_L \gt sum_K$ 的位置便是 $1$ 的起点。  
写完之后发现在第三个大样例中挂了几个点，于是开始观察样例。  
发现输出与正确答案的 $01$ 分界点的位置只差了 $1$ ，并且正确答案的 $0$ 总是少 $1$，所以猜测在某种情况下，分界点 $p$ 可以向前移动一位。  
测试大样例，发现确实如此。  
继续观察样例三，也许 $p$ 能向前移动的条件是 $a_{p-1} \gt a_p$？但样例中就有反例：

```
4
1 2 1 3
```
在这个样例中， $p$ 的初始位置是 $3$。因为 $a_2 \gt a_3$，所以 $p$ 就向前移动了一位，但这样的答案便是错误的。  
为什么当 $a_{p-1} \gt a_p$ 时，$p$ 向前移一位不一定对呢？观察这个样例，发现即使小 L 使用 $a_2$ 占领了 $a_3$，小 K 仍然可以使用 $a_4$ 重新收回失地。所以为了防止小 K 反攻，$a_{p-1}+a_p$ 应当大于 $a_{p+1}$。  
保险起见，要开 $long long$。
```cpp
#include<bits/stdc++.h>

#define int long long

using namespace std;

const int MAXN = 1e5 + 5;

int n;
int a[MAXN], sum[MAXN];

void solve()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
        scanf ("%lld", a + i);
    for (int i = 1; i <= n; i++)
        sum[i] = sum[i - 1] + a[i];
    int p = 1;
    for (; p <= n - 1; p++)
        if (sum[p] > sum[n] - sum[p])
            break;
    if (a[p - 1] > a[p] && a[p - 1] + a[p] > a[p + 1])
        p--;
    for (int i = 1; i <= n - 1; i++)
        if (i < p)
            cout << 0;
        else
            cout << 1;
    cout << "\n";
}

signed main()
{
    int T;
    cin >> T;
    while (T--)
        solve();
    return 0;
}
```
