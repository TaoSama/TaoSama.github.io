---
title: HDU 5658 CA Loves Palindromic（Manacher | 回文树）
categories:
  - 字符串
  - Manacher/回文树
  - 
tags:
  - Manacher
  - 回文树
date: 2016-04-11 02:03:10
toc: 
---
题意：
>$N\le 10^3的字符串，Q\le 10^5次询问$
$每次询问[l,\ r]区间本质不同的回文子串有几个，即不完全相同的回文子串$

<!-- more -->

分析：
>$Manacher做法，就离线询问，然后判断回文，哈希判重$
$回文树就更暴力了，直接预处理ans[l][r]$
$回文树的学习直接上鸟神博客$，[Palindromic Tree——回文树【处理一类回文串问题的强力工具】](http://blog.csdn.net/u013368721/article/details/42100363)

Manacher代码：
```cpp
//
//  Created by TaoSama on 2016-04-08
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iomanip>
#include <iostream>
#include <map>
#include <queue>
#include <string>
#include <set>
#include <vector>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

struct BIT {
    int n, b[N];
    void init(int _n) {
        n = _n;
        memset(b, 0, sizeof b);
    }
    void add(int i, int v) {
        for(; i; i -= i & -i) b[i] += v;
    }
    int sum(int i) {
        int ret = 0;
        for(; i <= n; i += i & -i) ret += b[i];
        return ret;
    }
} bit;

struct Manacher {
    char s[N << 1];
    int n, p[N << 1];
    void init(char* a) {
        s[0] = '@'; s[1] = '#'; n = 2;
        for(int i = 1; a[i]; ++i)
            s[n++] = a[i], s[n++] = '#';
        s[n] = 0;
    }
    int gao() {
        int mx = 0, id, ret = 0;
        for(int i = 1; i < n; ++i) {
            p[i] = mx > i ? min(mx - i, p[2 * id - i]) : 1;
            while(s[i - p[i]] == s[i + p[i]]) ++p[i];
            if(mx < i + p[i]) mx = i + p[i], id = i;
            ret = max(ret, p[i] - 1);
        }
        return ret;
    }
    bool ok(int l, int r) {
        l <<= 1; r <<= 1;
        int k = l + r >> 1;
        return k + p[k] - 1 >= r;
    }
} manacher;

typedef unsigned long long ULL;
const ULL seed[2] = {MOD, MOD + 2};
struct Hash {
    typedef pair<ULL, ULL> Type;
    ULL power[2][N], h[2][N];

    void init(char* a) {
        for(int i = 0; i < 2; ++i) {
            power[i][0] = 1; h[i][0] = 0;
            for(int j = 1; a[j]; ++j) {
                power[i][j] = power[i][j - 1] * seed[i];
                h[i][j] = h[i][j - 1] * seed[i] + a[j];
            }
        }
    }

    Type get(int l, int r) { // [l, r]
        Type ret;
        ret.first = h[0][r] - h[0][l - 1] * power[0][r - l + 1];
        ret.second = h[1][r] - h[1][l - 1] * power[1][r - l + 1];
        return ret;
    }
} hsh;

int n, q;
const int Q = 1e5 + 10;
int ans[Q];
char a[N];
vector<pair<int, int> > qs[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%s", a + 1);
        n = strlen(a + 1);

        hsh.init(a);
        manacher.init(a);
        manacher.gao();

        scanf("%d", &q);
        for(int i = 1; i <= n; ++i) qs[i].clear();
        for(int i = 1; i <= q; ++i) {
            int l, r; scanf("%d%d", &l, &r);
            qs[r].push_back({l, i});
        }

        bit.init(n);
        map<Hash::Type, int> mp;
        for(int i = 1; i <= n; ++i) {
            for(int j = 1; j <= i; ++j) {
                if(!manacher.ok(j, i)) continue; //not palindromic
                Hash::Type h = hsh.get(j, i);
                if(mp.count(h)) bit.add(mp[h], -1);
                bit.add(j, 1);
                mp[h] = j;
            }
            for(auto& q : qs[i]) ans[q.second] = bit.sum(q.first);
        }
        for(int i = 1; i <= q; ++i) printf("%d\n", ans[i]);
    }
    return 0;
}
```

回文树代码：
```cpp
//
//  Created by TaoSama on 2016-04-08
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iomanip>
#include <iostream>
#include <map>
#include <queue>
#include <string>
#include <set>
#include <vector>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

// last:= 指向添加一个字符后形成的最长回文串的节点
// s[i]:= 第 i 次添加的字符 n:= s 数组时针
// fail[i]:= i 失配后跳转到的 i 表示的最长回文串的最长真后缀回文串的节点
// cnt[i]:= 以 i 表示的最长回文串的右端点为右端点的回文串个数
// dif[i]:= i 表示的本质不同的回文串个数 （需要重新统计）
struct PalindromicTree {
    static const int M = 1e3 + 10, S = 26;
    int n, sz, last;
    int nxt[M][S], fail[M], len[M], s[M];
    int cnt[M], dif[M];

    int newNode(int l) {
        len[sz] = l;
        cnt[sz] = dif[sz] = 0;
        memset(nxt[sz], 0, sizeof nxt[sz]);
        return sz++;
    }

    void init() {
        sz = last = 0;
        newNode(0); newNode(-1);
        s[n = 0] = -1; // 无关字符减少特判
        fail[0] = 1;
    }

    int getFail(int u) {
        while(s[n - len[u] - 1] != s[n]) u = fail[u];
        return u;
    }

    void add(int c) {
        s[++n] = c;
        int u = getFail(last); // 找到这个回文串的匹配位置
        int& v = nxt[u][c];
        if(!v) {
            int cur = newNode(len[u] + 2);
            fail[cur] = nxt[getFail(fail[u])][c];
            v = cur;
            cnt[v] = cnt[fail[v]] + 1;
        }
        ++dif[v];
        last = v;
    }

    void count() {
        //父亲累加儿子，如果 fail[v]=u ，则 u 一定是 v 的子回文串
        for(int i = sz - 1; ~i; --i) dif[fail[i]] += dif[i];
    }
} pt;

int n, q;
char a[N];
int ans[N][N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%s", a + 1);
        n = strlen(a + 1);

        for(int i = 1; i <= n; ++i) {
            pt.init();
            for(int j = i; j <= n; ++j) {
                pt.add(a[j] - 'a');
                ans[i][j] = pt.sz - 2;
            }
        }

        scanf("%d", &q);
        while(q--) {
            int l, r; scanf("%d%d", &l, &r);
            printf("%d\n", ans[l][r]);
        }
    }
    return 0;
}
```