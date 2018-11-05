---
title: HDU 5785 Interesting（Manacher | 回文树）
categories:
  - 字符串
  - Manacher/回文树
  - 
tags:
  - Manacher
  - 回文树
  - 
date: 2016-08-04 10:30:10
toc: 
---

题意：
>$给定N\le 10^6的字符串，现在寻找所有三元组(i,\ j,\ k)，1\le i\le j<k\le N$
$使得s[i\ldots j]和s[j+1\ldots k]都是回文串，求\sum\sum i\times k\ mod\ 10^9+7$

<!-- more -->
分析：
>$赛上做法比较那啥，回文树硬肝的，a、b为某2个回文串长度$
$[i\ldots j-1]、[j\ldots k]$
$\sum\sum i\times k=\sum\sum (j-1-a+1)(j+b-1)$
$=\sum\sum (j-a)(j+b-1)$
$=preCnt\times sufCnt\times j^2$
$+(preCnt\times (sufSum-sufCnt)-preSum\times sufCnt)j$
$-preSum\times(sufSum-sufCnt)$
$preCnt[i]:=以i结尾的回文串个数，preSum[i]:=以i结尾的回文串的长度和$
$suf同理$
$然后回文树预处理一下就做完了，时间复杂度O(n)$

---
>$\sum\sum i\times k=\sum i\times \sum k$
$Manacher的话直接预处理sum[2][i]:=0开头，1结尾的右/左端点的和$
$对于一个以i为中心的延伸距离为p[i]的最长回文串$
$显然l=i-(p[i]-1)，r=i+p[i]-1$
$对于sum[0][i]，i\in[l,\ i]右端点的贡献是r\sim i$
$这是一个首项为r，公差为-1的等差数列$
$由于所有更新都是静态的，窝萌可以partial\ sum搞一波$
$对于一个更新[L,\ R]，首项为a，公差为d的等差数列$
$delta数组记录公差，sum数组记录结果$
`delta[L+1] += d // [L+1, R]的区间有公差`
`delta[R+1] -= d`
`sum[L] += a`
`sum[R+1] -= a + (R-L)*d //这里要去掉公差累计的影响`
$累计的时候累计上公差就可以了$
$直接在Manacher数组上搞就行，最后除2就好$
$然后就做完了$

代码一:
```cpp
//
//  Created by TaoSama on 2016-08-02
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

struct PalindromicTree {
    static const int M = 1e6 + 10, S = 26;
    int n, sz, last;
    int nxt[M][S], fail[M], len[M];
    char s[M];
    int cnt[M], sum[M];
    int newnode(int l) {
        len[sz] = l;
        sum[sz] = cnt[sz] = 0;
        memset(nxt[sz], 0, sizeof(nxt[sz]));
        return sz++;
    }
    void init() {
        sz = last = 0;
        newnode(0); newnode(-1);
        s[n = 0] = -1;
        fail[0] = 1;
    }
    int getfail(int u) {
        while(s[n - len[u] - 1] != s[n]) u = fail[u];
        return u;
    }
    pair<int, int> add(int c) {
        s[++n] = c;
        int u = getfail(last);
        int& v = nxt[u][c];
        if(!v) {
            int cur = newnode(len[u] + 2);
            fail[cur] = nxt[getfail(fail[u])][c];
            v = cur;
//            pr(len[fail[v]]); prln(len[v]);
            cnt[v] = cnt[fail[v]] + 1;
            sum[v] = sum[fail[v]] + len[v];
            if(sum[v] >= MOD) sum[v] -= MOD;
//            prln(sum[v]);
        }
        last = v;
        return {cnt[v], sum[v]};
    }
} pt;

int n;
char s[N];
int preCnt[N], preSum[N];

typedef long long LL;
LL mul(LL x, LL y) {
    return x * y % MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%s", s + 1) == 1) {
        n = strlen(s + 1);
        pt.init();
        for(int i = 1; i <= n; ++i) {
            auto ret = pt.add(s[i] - 'a');
            tie(preCnt[i], preSum[i]) = ret;
        }

        pt.init();
        LL ans = 0;
        for(int i = n; i > 1; --i) {
            auto ret = pt.add(s[i] - 'a');
            int sufCnt, sufSum;
            tie(sufCnt, sufSum) = ret;
//            prln(i);
//            printf("%d %d %d %d\n", preCnt[i - 1], preSum[i - 1], sufCnt, sufSum);

            LL sqI = mul(mul(mul(i, i), preCnt[i - 1]), sufCnt);
            LL mid = mul(preCnt[i - 1], sufSum - sufCnt) -
                     mul(sufCnt, preSum[i - 1]);
            mid %= MOD;
            mid = mul(mid, i);
            LL rht = mul(sufSum - sufCnt, preSum[i - 1]);
            ans += sqI + mid - rht;
            ans %= MOD;
//            prln(ans);
        }
        ans = (ans + MOD) % MOD;
        printf("%I64d\n", ans);
    }

    return 0;
}
```

代码二：
```cpp
//
//  Created by TaoSama on 2016-08-03
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

// 原串 a[i]:         w   a   a   b   w   s   w   f   d
// 新串 s[i]:       # w # a # a # b # w # s # w # f # d #
// 辅助数组 p[i]:   1 2 1 2 3 2 1 2 1 2 1 4 1 2 1 2 1 2 1
// p[i]   := 新串以 s[i] 为中心向右延伸的回文距离 + 1 （自己）
// p[i]-1 := 原串以 s[i] 为中心的回文长度

int n;
char s[N];

struct Manacher {
    static const int M = N << 1;
    char s[M];
    int n, p[M];
    int delta[2][M], sum[2][M];

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

    inline void add(int& x, int y) {
        if(y < 0) y += MOD;
        if((x += y) >= MOD) x -= MOD;
    }
    //0->start 1->end
    void process() {
        memset(delta, 0, sizeof delta);
        memset(sum, 0, sizeof sum);
        for(int i = 1; i < n; ++i) {
            int r = i + p[i] - 1, l = i - (p[i] - 1);
            add(sum[0][l], r);
            add(sum[0][i + 1], -r - (i - l) * (-1)); //+ r ~ i
            add(delta[0][l + 1], -1);
            add(delta[0][i + 1], 1); //d + -1

            add(sum[1][i], i);
            add(sum[1][r + 1], -i - (r - i) * (-1)); //+ i ~ l
            add(delta[1][i + 1], -1);
            add(delta[1][r + 1], 1); //d + -1
        }
        for(int i = 1; i < n; ++i) {
            for(int j = 0; j < 2; ++j) {
                add(delta[j][i], delta[j][i - 1]);
                add(sum[j][i], sum[j][i - 1]);
                add(sum[j][i], delta[j][i]);
            }
        }
    }

    bool ok(int l, int r) {
        l <<= 1; r <<= 1;
        int k = l + r >> 1;
        return k + p[k] - 1 >= r;
    }
} ma;

int quick(int x, int n) {
    int ret = 1;
    for(; n ; n >>= 1) {
        if(n & 1) ret = 1LL * ret * x % MOD;
        x = 1LL * x * x % MOD;
    }
    return ret;
}

const int invTwo = 500000004;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%s", s + 1) == 1) {
        ma.init(s);
        ma.gao();
        ma.process();
        n = strlen(s + 1);

        int* preSum = ma.sum[0], *sufSum = ma.sum[1];

        int ans = 0;
        for(int i = 1; i < n; ++i) {
            sufSum[i << 1] = 1LL * sufSum[i << 1] * invTwo % MOD;
            preSum[i + 1 << 1] = 1LL * preSum[i + 1 << 1] * invTwo % MOD;
            ans += 1LL * sufSum[i << 1] * preSum[i + 1 << 1] % MOD;
            if(ans >= MOD) ans -= MOD;
        }
        printf("%d\n", ans);
    }

    return 0;
}
```
