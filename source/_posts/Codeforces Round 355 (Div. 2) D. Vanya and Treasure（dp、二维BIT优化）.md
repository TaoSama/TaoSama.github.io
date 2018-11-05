---
title: Codeforces Round 355 (Div. 2) D. Vanya and Treasure（dp、二维BIT优化）
categories:
  - 动态规划
  - 数据结构优化
  - 
tags:
  - 二维BIT 
  - 
date: 2016-06-03 01:20:10
toc: 
---

题意：
>$N，M\le 300，P\le N\times M，给定一个N\times M图，每个格子A_{ij}是1\sim P的数字$
$从(1,\ 1)出发，两个格子的距离定义为曼哈顿距离，按顺序取1\sim P的数字$
$问最短路是多少$

<!-- more -->
分析：
>$我们有一个显然的dp状态，f[x][y]:=到达(x,\ y)这个格子，并且取到数字的最短路$
$f[x_2][y_2]=min\{\ f[x_1][y_1]+dis((x_1,y_1),\ (x_2,y_2))\ \},A[x_1][y_1]=i-1,A[x_2][y_2]=i$
$裸的转移是O(nm)的，总复杂度就是O((nm)^2)了，显然是不行的，考虑优化$
$优化一：$
$令cnt[i]:=i这个数字的个数，当cnt[i-1]\times cnt[i]\le n\times m的时候直接更新，否则bfs一波$
$这个时间复杂度是O(nm\sqrt{nm})，如果有人懂详细证明请教我。。$
$优化二：$
$我们展开这个dp转移方程：$
$f[x_2][y_2]=min\{\ f[x_1][y_1]+abs(x_1-x_2)+abs(y_1-y_2)\ \}$
$发现对于曼哈顿距离其实有4种情况，根据x和y的大小关系，方便起见画个图：$
![](http://7xru22.com1.z0.glb.clouddn.com/16-6-3/79239680.jpg)
$A点为我们当前要更新的点，B0\sim B3为代表根据x和y大小划分的4个区域$
$我们可以得到新的转移方程：$
$UL - f[x_2][y_2] = min \{\ f[x_1][y_1] - x_1 - y_1 + x_2 + y_2\ \},\ B0区域$
$UR - f[x_2][y_2] = min \{\ f[x_1][y_1] + x_1 - y_1 - x_2 + y_2\ \},\ B1区域$
$BR - f[x_2][y_2] = min \{\ f[x_1][y_1] + x_1 + y_1 - x_2 - y_2\ \},\ B2区域$
$BL - f[x_2][y_2] = min \{\ f[x_1][y_1] - x_1 + y_1 + x_2 - y_2\ \},\ B3区域$
$对于当前点A(x_2,\ y_2)来说，转移方程中关于x_2和y_2的部分是常数$
$对于f[x_1][y_1]以及x_1和y_1的部分来说，是矩形区域，我们可以用二维线段树来维护$
$观察这4个区域，发现不是前缀区域就是后缀区域，所以我们用4个二维BIT就可以了$
$维护后缀区域只要转化一下就好了，具体见代码$

代码一:
```cpp
//
//  Created by TaoSama on 2016-06-02
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
const int N = 300 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, p;
int s[N][N];
typedef pair<int, int> P;

vector<P> G[N * N];
int f[N][N];

void getMin(int& x, int y) {
    if(x > y) x = y;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    scanf("%d%d%d", &n, &m, &p);
    for(int i = 1; i <= n; ++i) {
        for(int j = 1; j <= m; ++j) {
            scanf("%d", s[i] + j);
            G[s[i][j]].push_back({i, j});
            f[i][j] = s[i][j] == 1 ? i + j - 2 : INF;
        }
    }

    for(int i = 1; i < p; ++i) {
        vector<P>& cur = G[i], &nxt = G[i + 1];
        int delta = cur.size() * nxt.size();
        if(delta <= n * m) {
            for(P& u : cur) {
                for(P& v : nxt) {
                    int d = abs(u.first - v.first) + abs(u.second - v.second);
                    getMin(f[v.first][v.second], f[u.first][u.second] + d);
                }
            }
        } else {
            static vector<P> q; q.clear();
            static int d[N][N], in[N][N];
            memset(d, 0x3f, sizeof d);
            memset(in, false, sizeof in);

            for(P& u : cur) {
                q.push_back(u);
                in[u.first][u.second] = true;
                d[u.first][u.second] = f[u.first][u.second];

            }

            static int dir[][2] = { -1, 0, 0, -1, 1, 0, 0, 1};
            for(int j = 0; j < q.size(); ++j) {
                P u = q[j];
                in[u.first][u.second] = false;
                for(int k = 0; k < 4; ++k) {
                    P v = u;
                    v.first += dir[k][0];
                    v.second += dir[k][1];
                    if(!s[v.first][v.second]) continue;
                    if(d[v.first][v.second] > d[u.first][u.second] + 1) {
                        d[v.first][v.second] = d[u.first][u.second] + 1;
                        if(!in[v.first][v.second]) {
                            in[v.first][v.second] = true;
                            q.push_back(v);
                        }
                    }
                }
            }
            for(P& v : nxt) f[v.first][v.second] = d[v.first][v.second];
        }
    }
    int x = G[p][0].first, y = G[p][0].second;
    printf("%d\n", f[x][y]);

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```

---
代码二：
```cpp
//
//  Created by TaoSama on 2016-06-02
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
#include <bitset>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 300 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, p;
int s[N][N], f[N][N];
typedef pair<int, int> P;
vector<P> G[N * N];

struct BIT {
    int n, b[N][N];
    int timStp, vis[N][N];
    void init(int _n) {
        n = _n;
        timStp = 1;
        memset(vis, 0, sizeof vis);
    }
    void newOne() {
        ++timStp;
    }
    void update(int x, int y, int v) {
        for(int i = x; i <= n; i += i & -i) {
            for(int j = y; j <= n; j += j & -j) {
                if(vis[i][j] != timStp) vis[i][j] = timStp, b[i][j] = INF;
                b[i][j] = min(b[i][j], v);
            }
        }
    }
    int query(int x, int y) {
        int ret = INF;
        for(int i = x; i; i -= i & -i)
            for(int j = y; j; j -= j & -j)
                if(vis[i][j] == timStp) ret = min(ret, b[i][j]);
        return ret;
    }
} bit[4];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    scanf("%d%d%d", &n, &m, &p);
    for(int i = 1; i <= n; ++i) {
        for(int j = 1; j <= m; ++j) {
            scanf("%d", s[i] + j);
            G[s[i][j]].push_back({i, j});
        }
    }

    memset(f, 0x3f, sizeof f);
    int nm = max(n, m);
    for(int i = 0; i < 4; ++i) bit[i].init(nm);
    //UL - f[x2][y2] = min { f[x1][y1] - x1 - y1 + x2 + y2 }
    //UR - f[x2][y2] = min { f[x1][y1] + x1 - y1 - x2 + y2 }
    //BR - f[x2][y2] = min { f[x1][y1] + x1 + y1 - x2 - y2 }
    //BL - f[x2][y2] = min { f[x1][y1] - x1 + y1 + x2 - y2 }
    for(int i = 0; i < G[1].size(); ++i) {
        int x = G[1][i].first, y = G[1][i].second;
        f[x][y] = x + y - 2;
        bit[0].update(x, y, f[x][y] - x - y);
        bit[1].update(nm - x + 1, y, f[x][y] + x - y);
        bit[2].update(nm - x + 1, nm - y + 1, f[x][y] + x + y);
        bit[3].update(x, nm - y + 1, f[x][y] - x + y);
    }

    for(int i = 2; i <= p; ++i) {
        for(int j = 0; j < G[i].size(); ++j) {
            int x = G[i][j].first, y = G[i][j].second, val = INF;
            val = min(val, bit[0].query(x, y) + x + y);
            val = min(val, bit[1].query(nm - x + 1, y) - x + y);
            val = min(val, bit[2].query(nm - x + 1, nm - y + 1) - x - y);
            val = min(val, bit[3].query(x, nm - y + 1) + x - y);
            f[x][y] = val;
        }

        for(int j = 0; j < 4; ++j) bit[j].newOne();
        for(int j = 0; j < G[i].size(); ++j) {
            int x = G[i][j].first, y = G[i][j].second;
            bit[0].update(x, y, f[x][y] - x - y);
            bit[1].update(nm - x + 1, y, f[x][y] + x - y);
            bit[2].update(nm - x + 1, nm - y + 1, f[x][y] + x + y);
            bit[3].update(x, nm - y + 1, f[x][y] - x + y);
        }
    }

    int x = G[p][0].first, y = G[p][0].second;
    printf("%d\n", f[x][y]);

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```