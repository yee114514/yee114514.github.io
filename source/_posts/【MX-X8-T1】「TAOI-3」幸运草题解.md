---
title: 【MX-X8-T1】「TAOI-3」幸运草题解
date: 2025-01-31
tags: 题解
---

题目链接：[洛谷P11642](https://www.luogu.com.cn/problem/P11642)



本题的关键在于$l$和$r$该如何选取。考虑建立数组$b$，其中$b_i=x-a_i$，代表如果将$a_i$换为$x$，将会对答案造成正影响还是负影响。所以$b$的最大子段和的$l$、$r$，就是答案的$l$、$r$。  
值得注意的是，经过对拍，发现$n=1$和$x=0$时程序的正确性有问题，所以我加了特判。但很有可能是我的码太史了所以才有问题。  

```cpp
#include<bits/stdc++.h>

#define int long long

using namespace std;

const int MAXN=1e5+5;

int a[MAXN],b[MAXN],f[MAXN];

signed main()
{
    int n,x;
    cin>>n>>x;
    for(int i=1;i<=n;i++)
        scanf("%lld",a+i),b[i]=x-a[i];
    if(n==1)
    {
        if(a[1]>x)
            cout<<a[1];
        else
            cout<<x;
        return 0;
    }
    if(x==0)
    {
        int ans=0;
        for(int i=1;i<=n;i++)
            ans+=a[i];
        cout<<ans;
        return 0;
    }
    int ans=LONG_MIN,l,r;
    for(int i=1;i<=n;i++)
    {
        f[i]=max(f[i-1],0LL)+b[i];
        if(ans<f[i])
            ans=f[i],r=i;
    }
    for(l=r;l>=1;l--)
        if(f[l]!=f[l-1]+b[l])
            break;
    for(int i=l;i<=r;i++)
        a[i]=x;
    ans=0;
    for(int i=1;i<=n;i++)
        ans+=a[i];
    cout<<ans;
	return 0;
}
```
