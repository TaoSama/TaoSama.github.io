---
title: BestCoder Round 92
categories:
  - 套题
  - 
  - 
tags:
  - 
  - 
date: 2017-04-05 01:04:10
toc: true
---

好久没写题解了。。感觉还是经常写点文字。题解勉强算是吧(雾

<!-- more -->

### 1001 Skip the Class
直接对于每种课程维护最大值和次大值即可

### 1002 Count the Sheep
题意：
$给定N\le 10^5个男羊，M\le 10^5个女羊，K\le 10^5个朋友关系$
$问满足A-B、B-C、C-D是朋友关系且A、B、C、D各不相同的，A-B-C-D这样序列的方案数$

分析：
$直接枚举B-C边，然后统计下两边的度就好了，别忘了减去自己$

### 1003 Girls Love 233
题意：
$给定长度N\le 100的由字符'2'和'3'构成的字符串$
$有\lfloor{M\over 2}\rfloor次操作次数，每次可以交换2个相邻的字符$
$最多能使这个字符串中有多少个子串"233"呢$

分析：
$官方题解给了一个很妙的dp$
$可以发现答案其实只跟'2'有关，即'3'和'3'换是毫无意义的$
$于是我们可以抠出来所有'2'的位置，那么只对'2'有交换的花费$
$接下来考虑dp，f[i][j][k][3]:=$
$选取了i个'2'，j个'3'，使用了k次交换，状态是s的最多子串数$
$s状态显然有i\in [0,3):'2'后面有i个'3'，如果有第一次有2个'3'显然答案+1$
$最终ans=\displaystyle\max_{k,\ s}\{ f[c2][c3][k][s]\}$

代码:
```cpp
//
//  Created by TaoSama on 2017-02-27
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
const int N = 1e2 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
char s[N];
int f[N][N][N / 2][3];

void getMax(int& x, int y) {
    if(x < y) x = y;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        vector<int> p(1, 0);
        scanf("%d%d%s", &n, &m, s + 1);  m >>= 1;
        for(int i = 1; i <= n; ++i) if(s[i] == '2') p.push_back(i);
        int c2 = p.size() - 1, c3 = n - c2;

        for(int i = 0; i <= c2; ++i)
            for(int j = 0; j <= c3; ++j)
                for(int k = 0; k <= m; ++k)
                    for(int z = 0; z < 3; ++z)
                        f[i][j][k][z] = -INF;
        f[0][0][0][2] = 0;
        for(int i = 0; i <= c2; ++i) {
            for(int j = 0; j <= c3; ++j) {
                for(int k = 0; k <= m; ++k) {
                    for(int z = 0; z < 3; ++z) {
                        if(f[i][j][k][z] < 0) continue;
                        if(i < c2) {
                            int nk = k + abs(i + j + 1 - p[i + 1]);
                            if(nk <= m) getMax(f[i + 1][j][nk][0], f[i][j][k][z]);
                        }
                        if(j < c3) {
                            int nz = z, one = 0;
                            if(nz != 2) {
                                if(++nz == 2) ++one;
                            }
                            getMax(f[i][j + 1][k][nz], f[i][j][k][z] + one);
                        }
                    }
                }
            }
        }

        int ans = 0;
        for(int k = 0; k <= m; ++k)
            for(int z = 0; z < 3; ++z)
                getMax(ans, f[c2][c3][k][z]);
        printf("%d\n", ans);
    }

    return 0;
}
```

### 1004 Game Arrangement
题意：
$给定N\le 10^4段空闲时间[L_i,\ R_i]，1\le L_i\le R_i\le 10^9$
$给定M\le 10^4个游戏的感兴趣时段[l_i,\ r_i]，1\le l_i\le r_i\le 10^9，并且需要持续时间1\le d_i\le 10^9$
$对于i类游戏只能全在它的感兴趣时段，以及某段空闲时段才可以玩$
$问最多能玩游戏的次数$

分析:
$如果数据范围不是10^9的话，显然可以按时间来dp，就不多说了$
$可以考虑贪心，因为物品的价值都是1，就可以贪心了$
$对于当前时间，对某个游戏肯定选择持续短的，就可以用堆或者set来贪心了$
$首先把空闲时间和游戏按照起始时间排序$
$考虑枚举每一段空闲时间，首先把当前能玩的游戏全部加进去堆里$
$再把一把都玩不了的都删了，然后取出第一个能玩的$
$当前这个游戏能玩多久由三个东西限制，一个是空闲时间，一个是感兴趣时间$
$还有下一个游戏的开始时间来限制$
$肯定尝试下取整个是一定可以的$
$然后你以为就对了？？显然不是，我还可以后延续到下一个游戏的时间$
$所以就考虑再玩一个，但是只是尝试，所以拆成两半，一个是现在玩一些$
$剩下的时间成为一个新的游戏塞进去，之后直接尝试下一个游戏就可以了$
$这样贪心就对了$
$注意一下边界的细节，时间复杂度是O(nlogn)$
$感谢bc大佬的代码$

```cpp
//
//  Created by TaoSama on 2017-02-27
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

int n, m;
struct Node {
    int l, r, d;
    bool operator<(const Node& r) const {
        return d > r.d;
    }
    void see() {
        pr(l); pr(r); prln(d);
    }
};

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        vector<Node> a, b;
        a.reserve(n); b.reserve(m);
        for(int i = 1; i <= n; ++i) {
            int l, r; scanf("%d%d", &l, &r);
            if(a.size() && a.back().r + 1 == l)
                a.back().r = r;
            else a.push_back({l, r, 0});
        }
        for(int i = 1; i <= m; ++i) {
            int l, r, d; scanf("%d%d%d", &l, &r, &d);
            b.push_back({l, r, d});
        }
        b.push_back({INF, -1, -1});
        sort(b.begin(), b.end(), [](const Node & a, const Node & b) {
            return a.l < b.l;
        });

        int ans = 0;
        priority_queue<Node> q;
        for(int i = 0, j = 0; i < a.size(); ++i) {
            int cur = a[i].l;
            for(; cur <= a[i].r;) {
                for(; b[j].l <= cur; ++j) q.push(b[j]); //insert
                while(q.size() && q.top().r - q.top().d + 1 < cur) q.pop(); //delete
                if(!q.size()) {cur = b[j].l; continue;}

                Node tp = q.top();
                int r = min(a[i].r, tp.r);
                int cnt = (min(r, b[j].l - 1) - cur + 1) / tp.d;
                ans += cnt;
                cur += cnt * tp.d;
                if(!cnt) {
                    int nxt = cur + tp.d - 1;
                    if(nxt <= r) q.push({b[j].l, nxt, nxt - b[j].l + 1});
                    cur = b[j].l;
                }
            }
        }
        printf("%d\n", ans);
    }

    return 0;
}
```