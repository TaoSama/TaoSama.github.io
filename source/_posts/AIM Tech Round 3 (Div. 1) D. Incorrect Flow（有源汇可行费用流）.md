---
title: AIM Tech Round 3 (Div. 1) D. Incorrect Flow（有源汇可行费用流）
categories:
  - 图论
  - 网络流
  - 
tags:
  - 可行流
  - 
  - 
date: 2016-08-26 16:36:10
toc: 
---

题意： 
>$N\le 100，M\le 100的流网络，0\le c_i,\ f_i\le 10^6$
$现在这个网络错了，可能c_i>f_i，也可能流量不平衡$
$现在要求你修改f_i和c_i使得流网络成为可行流，并且change=\sum |f_i'-f_i|+|c_i'-c_i|最小$
$求这个change$

<!-- more -->
分析：
>$对于无源汇的上下界可行流，新建源s，汇t，然后对于u\to v边，下界d，上界c$
$变成s\to t,\ 容量d；u\to v，容量c-d；v\to t，容量d$
$合并一下边就好了最后，如果有可行流，那么一定满流(下界一定满足)$
$对于有源汇的，添加t\to s，容量为INF的边，之后新建超源s'，超汇t'，再做上面的就可以了$
$显然添加那条边之后就抵消了原来的源汇的影响(即给他们加上了流量平衡)$

---
>$对于本题来说，有源汇的可行流，加t\to s，容量INF的边$
$先钦定下界为原始图的，统计一下流入流出的balance$
$接下来讨论f\le c的情况：$
$c-f次机会用1的费用使得流量+1$
$之后，INF次机会使用2的费用使得流量和容量一起+1$
$当然，也有f次机会使用费用1使得流量-1$
$对于f>c的情况：$
$因为只能运送流量，先直接把情况搞成合法，即预先用f-c的费用使得c=f(但事实上图没有变)$
$当然，可以INF次机会使用2的费用使得流量和容量一起+1$
$接下来，当然可以退回去，即f-c次机会0费用使得流量-1(相当于用预先加的代价)$
$之后，c次机会1费用使得流量-1$
$然后跑最小费用最大流就好了$

```cpp
//
//  Created by TaoSama on 2016-08-26
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const int M = 1e5 + 10;

struct Edge {
    int v, nxt, cost, cap;
} edge[M];
int head[N], cnt;

void addEdge(int u, int v, int c, int w1, int w2 = 0) {
    edge[cnt] = {v, head[u], c, w1};
    head[u] = cnt++;
    edge[cnt] = {u, head[v], -c, w2};
    head[v] = cnt++;
}

int in[N], d[N], delta[N], pre[N];
bool spfa(int s, int t) {
    deque<int> q; q.push_back(s);
    for(int i = s; i <= t; ++i) d[i] = INF, in[i] = false;
    delta[s] = INF; d[s] = 0; in[s] = true;
    while(q.size()) {
        int u = q.front(); q.pop_front();
        in[u] = false;
        for(int i = head[u]; ~i; i = edge[i].nxt) {
            int v = edge[i].v, cap = edge[i].cap, cost = edge[i].cost;
            if(cap > 0 && d[v] > d[u] + cost) {
                delta[v] = min(delta[u], cap);
                d[v] = d[u] + cost;
                pre[v] = i;
                if(!in[v]) {
                    in[v] = true;
                    if(q.size() && d[v] <= d[q.front()]) q.push_front(v);
                    else q.push_back(v);
                }
            }
        }
    }
    return d[t] != INF;
}

void minCostMaxFlow(int s, int t, int& flow, int& cost) {
    while(spfa(s, t)) {
        flow += delta[t];
        cost += d[t] * delta[t];
        for(int i, u = t; u != s; u = edge[i ^ 1].v) {
            i = pre[u];
            edge[i].cap -= delta[t];
            edge[i ^ 1].cap += delta[t];
        }
    }
}

int n, m;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &m) == 2) {
        cnt = 0; memset(head, -1, sizeof head);

        int ans = 0;
        int s = 0, t = n + 1;
        vector<int> balance(n + 1, 0);
        for(int i = 1; i <= m; ++i) {
            int u, v, c, f; scanf("%d%d%d%d", &u, &v, &c, &f);
            balance[u] -= f;
            balance[v] += f;

            if(f <= c) {
                addEdge(u, v, 1, c - f); //f +1
                addEdge(u, v, 2, INF); //f +1, c +1
                addEdge(v, u, 1, f); //f -1;
            } else {
                ans += f - c; //c->f first
                addEdge(u, v, 2, INF); //f +1, c +1
                addEdge(v, u, 0, f - c); //back
                addEdge(v, u, 1, c); //f -1
            }
        }
        addEdge(n, 1, 0, INF);
        for(int i = 1; i <= n; ++i) {
            if(balance[i] > 0) addEdge(s, i, 0, balance[i]);
            else addEdge(i, t, 0, -balance[i]);
        }
        int flow = 0;
        minCostMaxFlow(s, t, flow, ans);
        printf("%d\n", ans);
    }

    return 0;
}
```