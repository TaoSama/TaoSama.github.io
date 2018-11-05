---
title: Educational Codeforces Round 16（在线AC自动机、二进制分组）
categories:
  - 字符串
  - AC自动机
  - 
tags:
  - 二进制分组
  - 
  - 
date: 2016-09-08 1:00:10
toc: 
---

题意： 
>$给定N\le 3\times 10^5次操作，操作一个字符串集合$
$1\ s:向集合添加字符串s$
$2\ s:从集合删除字符串s$
$3\ s:查询字符串s在集合的所有字符串中出现了多少次$
$保证添加和删除操作合法，且\sum |S|\le 3\times 10^5$

<!-- more -->
分析：
>$首先兹磁这种操作的有ac自动机，但是ac自动机是个离线的数据结构$
$如何每次插入都build，那么总复杂度是1+2+\cdots+L=O(L^2)显然是不行的$
$考虑均摊一下build的复杂度，维护一大一小ac自动机big、small$
$每次添加和删除操作都往small的搞，每次都build，small如果大了就暴力合并到big上$
$设总长是L，假设1个阈值D\le L，那么小的build满的复杂度是1+2+\cdots+D=O(D^2)$
$显然一共有{L\over D}轮，这个复杂度是O({L\over D}\times D^2)=O(LD)$
$把small合并到big上，每次都build，这个复杂度第k次是kD$
$同理一共有{L\over D}轮，这个复杂度是D+2D+\cdots=O({L\over D}\times D+({L\over D})^2\times D)=O({L^2\over D})$
$那么总复杂度就是O(D^2+{L^2\over D})=O({L^2\over D})$
$显然当D=\sqrt L时，复杂度最好为O(L^{1.5})$

---
>$类似的我们还可以维护2^i大小的AC自动机，根据同样的复杂度分析$
$直观来讲每个字符移动了log次，所以总复杂度是O(LlogL)$

>$对于这种做法我们称之为二进制分组，对于这种贡献独立的题$
$我们以一个log的代价来让离线在线$

----
>$懒的写内存池了，直接用容器写数据结构了，跑的比数组还快。。$
$O2真可怕，看了下别人用了map写的比我的快了一倍$

$代码：$
```cpp
//
//  Created by TaoSama on 2016-08-23
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>
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
const int N = 3e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;

struct ACAutomata {
    static const int S = 26;
    int sz, root;
    vector<vector<int> > nxt;
    vector<int> fail, cnt;

    inline int idx(char c) {return c - 'a';}
    inline int newNode() {
        cnt.push_back(0);
        nxt.push_back(vector<int>(S, 0));
        return sz++;
    }
    void init() {
        sz = 0;
        nxt.clear();
        cnt.clear();
        fail.clear();
        root = newNode();
    }
    void insert(const char* s, int d) {
        int u = root;
        for(; *s; ++s) {
            int c = idx(*s);
            int& v = nxt[u][c];
            if(!v) v = newNode();
            u = v;
        }
        cnt[u] += d;
    }
    void build() {
        vector<int> q;
        fail.resize(nxt.size());
        fail[root] = root;
        for(int i = 0; i < S; ++i) {
            int& v = nxt[root][i];
            if(v) {
                fail[v] = root;
                q.push_back(v);
            } else v = root;
        }
        for(int k = 0; k < q.size(); ++k) {
            int u = q[k];
            for(int i = 0; i < S; ++i) {
                int& v = nxt[u][i];
                if(v) {
                    fail[v] = nxt[fail[u]][i];
                    cnt[v] += cnt[nxt[fail[u]][i]];
                    q.push_back(v);
                } else v = nxt[fail[u]][i];
            }
        }
    }
    LL query(const char* s) {
        LL ret = 0;
        int u = root;
        for(; *s; ++s) {
            int c = idx(*s);
            u = nxt[u][c];
            ret += cnt[u];
        }
        return ret;
    }
};

int q, op[N];
string s[N];

struct StaticToDynamic {
    static const int LOG = 20;
    ACAutomata ac[LOG];
    vector<int> g[LOG];

    void init() {
        for(int i = 0; i < LOG; ++i) {
            g[i].clear();
            ac[i].init();
        }
    }
    inline get(int x) {
        return x == 1 ? 1 : -1;
    }
    void add(int id) {
        int p = -1;
        for(int i = 0; i < LOG && !~p; ++i) if(g[i].empty()) p = i;
        g[p].push_back(id);
        ac[p].insert(s[id].c_str(), get(op[id]));

        for(int i = 0; i < p; ++i) {
            for(int id : g[i]) {
                g[p].push_back(id);
                ac[p].insert(s[id].c_str(), get(op[id]));
            }
            g[i].clear();
            ac[i].init();
        }
        ac[p].build();
    }
    LL query(int id) {
        LL ret = 0;
        for(int i = 0; i < LOG; ++i) ret += ac[i].query(s[id].c_str());
        return ret;
    }
} solver;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(cin >> q) {
        solver.init();
        for(int i = 1; i <= q; ++i) {
            cin >> op[i] >> s[i];
            if(op[i] <= 2) {
                solver.add(i);
            } else {
                cout << solver.query(i) << endl;
            }
        }
    }

    return 0;
}
```

---
别人的：
```cpp

#include <bits/stdc++.h>

using namespace std;

struct AC {
    vector<string> keys;
    vector<map<char, int>> trie;
    vector<long long> endp;
    vector<int> fail;
    AC() {
        trie.resize(1);
        endp.resize(1);
    }
    void reset() {
        keys.clear();
        trie.clear();
        endp.clear();
        fail.clear();
        trie.resize(1);
        endp.resize(1);
    }
    void add_string(string& s) {
        keys.push_back(s);
        int cur = 0;
        for(int i = 0; i < (int)s.length(); i++) {
            if(trie[cur].count(s[i]))
                cur = trie[cur][s[i]];
            else {
                cur = trie[cur][s[i]] = trie.size();
                trie.push_back(map<char, int>());
                endp.push_back(0);
            }
        }
        endp[cur]++;
    }
    void build() {
        fail.resize(trie.size());
        vector<pair<int, pair<int, char>>> Q;
        Q.push_back({0, {0, '\0'}});
        fail[0] = 0;
        for(int i = 0; i < (int)Q.size(); i++) {
            int u = Q[i].first;
            int p = Q[i].second.first;
            char c = Q[i].second.second;
            for(auto& it : trie[u])
                Q.push_back({it.second, {u, it.first}});
            if(u == 0)
                continue;
            int f = fail[p];
            while(f != 0 && !trie[f].count(c))
                f = fail[f];
            if(!trie[f].count(c) || trie[f][c] == u)
                fail[u] = 0;
            else
                fail[u] = trie[f][c];
            endp[u] += endp[fail[u]];
        }
    }
    long long count(string& s) {
        if(keys.empty())
            return 0;
        long long ret = 0;
        int cur = 0;
        for(int i = 0; i < (int)s.length(); i++) {
            while(cur != 0 && !trie[cur].count(s[i]))
                cur = fail[cur];
            if(trie[cur].count(s[i]))
                cur = trie[cur][s[i]];
            ret += endp[cur];
        }
        return ret;
    }
};

struct StaticToDynamic {
    AC ac[19];
    void add(string& s) {
        int k = 0;
        for(int i = 0; i < 19; i++) if(ac[i].keys.empty()) {
                k = i;
                break;
            }
        ac[k].add_string(s);
        for(int i = 0; i < k; i++) {
            for(auto& it : ac[i].keys)
                ac[k].add_string(it);
            ac[i].reset();
        }
        ac[k].build();
    }
    long long count(string& s) {
        long long ret = 0;
        for(int i = 0; i < 19; i++)
            ret += ac[i].count(s);
        return ret;
    }
} r, g;

char buf[300001];

int main() {
    int N;
    scanf("%d", &N);
    while(N--) {
        int t;
        scanf("%d%s", &t, buf);
        string s(buf, buf + strlen(buf));
        if(t == 1)
            r.add(s);
        else if(t == 2)
            g.add(s);
        else {
            printf("%I64d\n", r.count(s) - g.count(s));
            fflush(stdout);
        }
    }
    return 0;
}
```