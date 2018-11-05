---
title: Codeforces Round 408 (Div. 2)
categories:
  - 套题
  - 
  - 
tags:
  - 
  - 
date: 2017-04-12 04:34:10
toc: true
---

$C题直接读错题，被教育到死，DE其实好做，没仔细想就跑了。。$

<!-- more -->

### C. Bank Hacking
题意：
$N\le 10^5个点的树，点权，初始每个点黑色，现在要涂白$
$你的力量\ge 点权可以涂白，开始任选一点涂$
$涂白一个点导致和它之间相连的黑点点权+1，通过一个黑点相连的点权也+1$
$之后涂白一个点，必须保证它和一个白点相连$
$问最少需要多少力量怎么把所有点涂白$



分析：
$眼瞎没看到通过一个黑点相连，然后又没看到涂白的点必须和白点相连$
$这2个这么强的条件，你玩一下发现就是一圈一圈涂的，答案最多是maxA_i+2$
$然后你就枚举起点，相连的+1，其他的+2就可以了$
$map、multiset都怼不过去，按rank排序上BIT吧$
$其实维护一下maxA_i和maxA_i-1的个数就可以了，都没有说明答案maxA_i，不然有谁就谁+2$
$这里给出O(nlogn)的代码$

代码：
```cpp
//
//  Created by TaoSama on 2017-04-11
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
#define pr(x) cerr << #x << " = " << x << "  "
#define prln(x) cerr << #x << " = " << x << endl
const int N = 3e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, a[N];
vector<int> G[N];

struct BIT {
    int n, b[N];
    void init(int _n) {
        n = _n;
        memset(b, 0, sizeof b);
    }
    void add(int i, int v) {
        for(; i <= n; i += i & -i) b[i] += v;
    }
    int sum(int i) {
        int ret = 0;
        for(; i; i -= i & -i) ret += b[i];
        return ret;
    }
    int kth(int k) {
        int ret = 0;
        for(int i = 18; ~i; --i) {
            int x = 1 << i;
            if(ret + x <= n && b[ret + x] < k) {
                k -= b[ret + x];
                ret += x;
            }
        }
        return ret + 1;
    }
} bit;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 1; i <= n; ++i) G[i].clear();

        vector<int> xs;
        for(int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            xs.push_back(a[i]);
        }
        for(int i = 1; i < n; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }

        sort(xs.begin(), xs.end());
        xs.resize(unique(xs.begin(), xs.end()) - xs.begin());
        auto getRank = [&](int x) {
            return lower_bound(xs.begin(), xs.end(), x) - xs.begin() + 1;
        };

        int ans = INF;
        bit.init(xs.size());
        for(int i = 1; i <= n; ++i) bit.add(getRank(a[i]), 1);
        for(int i = 1; i <= n; ++i) {
            int cur = a[i];
            bit.add(getRank(a[i]), -1);
            for(int v : G[i]) {
                bit.add(getRank(a[v]), -1);
                cur = max(cur, a[v] + 1);
            }
            int k = bit.sum(xs.size());
            if(k) {
                int idx = bit.kth(k);
                cur = max(cur, xs[idx - 1] + 2);
            }
            ans = min(ans, cur);

            bit.add(getRank(a[i]), 1);
            for(int v : G[i]) bit.add(getRank(a[v]), 1);
        }
        printf("%d\n", ans);
    }

    return 0;
}


```

### D. Police Stations
题意：
$N\le 10^5的树，1\le K\le 10^5个标记点，给定距离0\le D < N$
$问最多删去几条边使得，每个点到标记点的距离还能不超过D$
$保证有解$

分析:
$保证有解，所以其实就相当于每个标记点所在的连通块与其他的连接对半切$
$找到这个对半切的边就好了，答案一定是标记点数-1$
$直接从标记点开始bfs就好了，如果碰到访问过的点，切掉就ok，一定是对半切的$
$毕竟bfs是按照level来的，这样实现非常帅气啊$

```cpp
//
//  Created by TaoSama on 2017-04-11
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
#define pr(x) cerr << #x << " = " << x << "  "
#define prln(x) cerr << #x << " = " << x << endl
const int N = 3e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, d;
bool cut[N], vis[N];
vector<pair<int, int>> G[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d%d", &n, &m, &d) == 3) {
        vector<pair<int, int>> q; q.reserve(n);
        for(int i = 1; i <= m; ++i) {
            int x; scanf("%d", &x);
            q.push_back({x, 0});
        }

        for(int i = 1; i <= n; ++i) G[i].clear();
        for(int i = 1; i < n; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            G[u].push_back({v, i});
            G[v].push_back({u, i});
        }

        memset(vis, 0, sizeof vis);
        memset(cut, 0, sizeof cut);
        for(int i = 0; i < q.size(); ++i) {
            int u, fa; tie(u, fa) = q[i];
            if(vis[u]) continue;
            vis[u] = true;
            for(const auto& e : G[u]) {
                int v, id; tie(v, id) = e;
                if(v == fa) continue;
                if(vis[v]) {
                    cut[id] = true;
                    continue;
                }
                q.push_back({v, u});
            }
        }

        vector<int> ans;
        for(int i = 1; i < n; ++i) if(cut[i]) ans.push_back(i);
        printf("%d\n", ans.size());
        for(int i = 0; i < ans.size(); ++i)
            printf("%d%c", ans[i], " \n"[i + 1 == ans.size()]);
    }

    return 0;
}
```

### E. Exam Cheating
题意：
$N\le 10^3行，有2个人，各会一些其中一些行，主人公看P\le 10^3次$
$每次选择一个人看连续K\le 50行，问主人公最多能会多少行$

分析:
$首先有一个暴力的dp，f[i][j][a][b]:=1\sim i行，看了j次$
$上一次看使得第一个人可以免费看a行，第二个人b行的最多会的行数$
$转移就枚举不看，看第一个人，看第二个人，都看$
$复杂度是O(npk^2)，显然会T$
$注意连续这个条件，2个人加起来最多看2\times \lceil {n\over k}\rceil次$
$所以复杂度变成了O(n\times 2\times \lceil {n\over k}\rceil k^2)=O(n^2k)$
$cf跑得很快就可以过了$

```cpp
//
//  Created by TaoSama on 2017-04-11
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
#define pr(x) cerr << #x << " = " << x << "  "
#define prln(x) cerr << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, k;
int f[2][1005][55][55];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d%d", &n, &m, &k) == 3) {
        vector<int> q(n + 1, 1);
        vector<int> a(n + 1, 0), b(n + 1, 0);
        int aCnt; scanf("%d", &aCnt);
        while(aCnt--) {
            int x; scanf("%d", &x);
            a[x] = 1;
        }
        int bCnt; scanf("%d", &bCnt);
        while(bCnt--) {
            int x; scanf("%d", &x);
            b[x] = 1;
        }

        int p = 0; memset(f[p], 0xc0, sizeof f[p]);
        f[p][0][0][0] = 0;
        auto getMax = [](int& x, int y) {if(x < y) x = y;};

        m = min(m, 2 * (n + k - 1) / k);
        for(int i = 1; i <= n; ++i) {
            memset(f[!p], 0xc0, sizeof f[!p]);
            for(int j = 0; j <= m; ++j) {
                for(int x = 0; x <= k; ++x) {
                    for(int y = 0; y <= k; ++y) {
                        int nj, nx, ny, val;
                        nj = j, nx = max(0, x - 1), ny = max(0, y - 1), val = (nx && a[i]) || (ny && b[i]);
                        getMax(f[!p][nj][nx][ny], f[p][j][x][y] + val);
                        nj = j + 1, nx = k, ny = max(0, y - 1), val = (nx && a[i]) || (ny && b[i]);
                        getMax(f[!p][nj][nx][ny], f[p][j][x][y] + val);
                        nj = j + 1, nx = max(0, x - 1), ny = k, val = (nx && a[i]) || (ny && b[i]);
                        getMax(f[!p][nj][nx][ny], f[p][j][x][y] + val);
                        nj = j + 2, nx = k, ny = k, val = (nx && a[i]) || (ny && b[i]);
                        getMax(f[!p][nj][nx][ny], f[p][j][x][y] + val);
                    }
                }
            }
            p = !p;
        }

        int ans = 0;
        for(int j = 0; j <= m; ++j) {
            for(int x = 0; x <= k; ++x) {
                for(int y = 0; y <= k; ++y) {
                    getMax(ans, f[p][j][x][y]);
                }
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```