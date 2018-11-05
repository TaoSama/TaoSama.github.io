---
title: BestCoder Round 93
categories:
  - 套题
  - 
  - 
tags:
  - 
  - 
date: 2017-04-12 00:34:10
toc: true
---

补题学套路。。

<!-- more -->

### 1001 MG loves gold

题意：
$N\le 10^5个数的序列，拆成尽快多的部分，使得每个部分不包含重复数字$

分析:
$直接贪心就好了，每次取尽可能长的不包含重复数字的，set判重即可$

代码：
```cpp
//
//  Created by TaoSama on 2017-04-01
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

int n, a[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        int ans = 0;
        set<int> mp;
        for(int i = 1, j; i <= n; i = j) {
            ++ans;
            mp.clear();
            for(j = i; j <= n && !mp.count(a[j]); ++j) mp.insert(a[j]);
        }
        printf("%d\n", ans);
    }

    return 0;
}
```


### 1002 MG loves apple

题意：
$给定1个N\le 10^5位的不含前导零的数字，现删去恰好0\le K<N个数字$
$使得剩下的数字，顺序不变，构成的合法数字，能被3整除$
$问是否可行$

分析：
$这个题跟CF\ EDU\ 18\ C的贪心做法类似$
$首先一个数能被3整除跟数字和sum能被3整除一致$
$接下来就统计一下cnt_i的个，\%3=i的数个数$
$首先特判数字中含0，且k=n-1的情况，CF那个题也是$

$之后就枚举非0数字，使得它作为第一位，存不存在一种合法方案使得sum\%3=0$
$这里要注意，这个非0数字是不能删掉的，他前面的都必须删掉，后面的就枚举一下$
$枚举0,1,3选取的个数，当然是\%3后的，之后判断need的数去掉这些之后能不能被3整除$
$然后判一判就好了，注意细节$
$复杂度O(3^3n)$

```cpp
//
//  Created by TaoSama on 2017-04-01
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
char a[N], r[N];
int cnt[3];

bool go(int mod, int need) {
    for(int a = 0; a < 3; ++a) { //1
        if(a > cnt[1]) continue;
        for(int b = 0; b < 3; ++b) { //2
            if(b > cnt[2]) continue;
            for(int c = 0; c < 3; ++c) { //0
                if(c > cnt[0]) continue;
                if((a + 2 * b) % 3 != mod) continue;
                if(a + b + c > need) continue;
                if((need - a - b - c) % 3 != 0) continue;
                int t = (cnt[1] - a) / 3 + (cnt[2] - b) / 3 + (cnt[0] - c) / 3;
                if(3 * t + a + b + c >= need) return true;
            }
        }
    }
    return false;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%s", &n, &m, a + 1);
        int sum = 0;
        for(int i = 0; i < 3; ++i) cnt[i] = 0;
        for(int i = 1; i <= n; ++i) {
            a[i] -= '0';
            r[i] = a[i] % 3;
            ++cnt[r[i]];
            sum += r[i];
        }

        bool ok = false;
        if(m == n - 1)
            for(int i = 1; i <= n && !ok; ++i) ok |= a[i] == 0;

        int need = m;
        for(int i = 1; i <= n && !ok; ++i) {
            int x = a[i];
            if(x) {
                --cnt[r[i]];
                ok |= go(sum % 3, need);
            } else --cnt[r[i]];
            sum -= r[i];
            if(--need < 0) break;
        }
        puts(ok ? "yes" : "no");
    }

    return 0;
}
```

### 1003 MG loves string

题意：
$给定一个26个小写字母的置换A，即进行一次变换，所有字符('a'+i)都会变成A_i$
$问一个长度是N\le 10^9随机字符串，变换到自身的期望变换次数$
$输出期望答案乘上26^n以后模10^9+7的结果$

分析：
$可以发现不同的置换的环的长度不超过6个，1+2+3+4+5+6>26$
$所以就枚举不同的置换的环的组合，至少出现一次的方案数$
$我们知道一个置换变回自己的次数是，每个环的长度的lcm$
$先统计出每个环长度的选取的字母的个数$
$f[sta]:=sta状态的环至少出现一次的方案数$
$算这个可以容斥来搞，随便选-非法的$
$g[sta]:=sta状态的环随便选的方案数，g[sta]=cnt[sta]^n$
$f[sta]=g[sta]-\displaystyle\sum_{s0\subset sta} f[s0]$
$之后乘上对应的lcm就可以了$
$复杂度为O(6^3logn)$

```cpp
//
//  Created by TaoSama on 2017-04-02
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

int n;
char a[27];
int f[1 << 6];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%s", &n, a);
        for(int i = 0; i < 26; ++i) a[i] -= 'a';

        map<int, int> mp;
        for(int i = 0; i < 26; ++i) {
            int cnt = 1;
            for(int j = a[i]; j != i; j = a[j]) ++cnt;
            ++mp[cnt];
        }
        vector<pair<int, int>> v(mp.begin(), mp.end());
        auto add = [&](int& x, int y) {if((x += y) >= MOD) x -= MOD;};
        auto quickPow = [&](int x, int y) {
            int ret = 1;
            for(; y; y >>= 1) {
                if(y & 1) ret = 1LL * ret * x % MOD;
                x = 1LL * x * x % MOD;
            }
            return ret;
        };

        int ans = 0;
        for(int s = 1; s < 1 << v.size(); ++s) {
            int sum = 0, lcm = 1;
            for(int i = 0; i < v.size(); ++i) {
                if(s >> i & 1) {
                    lcm = lcm / __gcd(lcm, v[i].first) * v[i].first;
                    sum += v[i].second;
                }
            }
            f[s] = quickPow(sum, n);
            for(int s0 = s & (s - 1); s0; s0 = (s0 - 1) & s) add(f[s], MOD - f[s0]);
            add(ans, 1LL * f[s] * lcm % MOD);
        }
        printf("%d\n", ans);
    }

    return 0;
}

```


### 1004 MG loves set

题意：
$如果一个集合所有元素的平方的和小于等于所有元素的和的平方，那么就称这个集合为“和谐集合”。$
$给定n\le 30个数，询问有多少个非空子集是“和谐集合”$

分析：
$现有一个集合S，则题目的条件为\displaystyle\sum_{x\in S} x^2\le (\displaystyle\sum_{x\in S} x)^2$
$移项则有，(\displaystyle\sum_{x\in S} x)^2-\displaystyle\sum_{x\in S} x^2\ge 0，这个就是2\displaystyle\sum_{x,\ y\in S,\ x<y}xy\ge 0$
$然后看到n=30，显然的折半枚举$
$令va=\displaystyle\sum_{x\in S} x，vb=2\displaystyle\sum_{x,\ y\in S,\ x<y}xy$
$那么上面那个式子由2个集合合并可以表示为，2\times va\times va'+vb+vb'\ge 0$
$把(va,\ vb)看成直线，(va',\ vb')看成点，那么就是求直线上方的点数，KDT优化即可$
$时间复杂度为O(2^{15}log2^{15})$

```cpp
//
//  Created by TaoSama on 2017-04-03
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

typedef long long LL;
const LL LLINF = 0x3f3f3f3f3f3f3f3fLL;
namespace KDT {
    const int M = 1 << 16, K = 2;
    int D;
    struct Point {
        LL d[K];
        inline LL& operator[](int k) {return d[k];}
        inline const LL& operator[](int k) const {return d[k];}
        inline bool operator<(const Point& p) const {
            return d[D] < p.d[D];
        }
    } a[M];
    struct Node {
        Point key, maxd, mind;
        Node* ch[2];
        int sz;
        inline void up() {
            sz = ch[0]->sz + ch[1]->sz + 1;
            for(int i = 0; i < K; ++i) {
                maxd[i] = max(maxd[i], ch[0]->maxd[i]);
                maxd[i] = max(maxd[i], ch[1]->maxd[i]);
                mind[i] = min(mind[i], ch[0]->mind[i]);
                mind[i] = min(mind[i], ch[1]->mind[i]);
            }
        }
    } pool[M], *ptr, *null, *root;

    inline bool onLine(const Point& p, const Point& q) {
        return 2 * p[0] * q[0] + p[1] + q[1] >= 0;
    }
    inline int h(Node* o, const Point& p) {
        int ret = 0;
        ret += onLine({o->mind[0], o->mind[1]}, p);
        ret += onLine({o->mind[0], o->maxd[1]}, p);
        ret += onLine({o->maxd[0], o->mind[1]}, p);
        ret += onLine({o->maxd[0], o->maxd[1]}, p);
        return ret;
    }

    inline Node* newNode(const Point& p) {
        ptr->key = p;
        ptr->ch[0] = ptr->ch[1] = null;
        for(int i = 0; i < K; ++i)
            ptr->maxd[i] = ptr->mind[i] = ptr->key[i];
        return ptr++;
    }

    void init() {
        ptr = pool;
        null = ptr++;
        null->sz = 0;
        null->ch[0] = null->ch[1] = null;
        for(int i = 0; i < K; ++i) {
            null->mind[i] = LLINF;
            null->maxd[i] = -LLINF;
        }
    }
    void build(Node*& o, int l, int r, int dim) {
        if(l > r) return;
        int m = l + r >> 1;
        D = dim;
        nth_element(a + l, a + m, a + r + 1);
        o = newNode(a[m]);
        build(o->ch[0], l, m - 1, (dim + 1) % K);
        build(o->ch[1], m + 1, r, (dim + 1) % K);
        o->up();
    }
    int query(Node* o, const Point& p) {
        if(o == null) return 0;
        int have = h(o, p);
        if(!have) return 0;
        if(have == 4) return o->sz;

        int ret = onLine(o->key, p);
        ret += query(o->ch[0], p);
        ret += query(o->ch[1], p);
        return ret;
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//    freopen("C:\\Users\\TaoSama\\Desktop\\out.txt", "w", stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        int n; scanf("%d", &n);
        vector<int> v(n);
        for(int& x : v) scanf("%d", &x);

        int hf = (n + 1) >> 1;
        for(int s = 0; s < 1 << hf; ++s) {
            LL va = 0, vb = 0;
            for(int i = 0; i < hf; ++i) {
                if(s >> i & 1) {
                    va += v[i];
                    vb += 1LL * v[i] * v[i];
                }
            }
            KDT::a[s + 1] = {va, va* va - vb};
        }

        KDT::init();
        KDT::Node*& root = KDT::root;
        KDT::build(root, 1, 1 << hf, 0);

        n >>= 1;
        int ans = 0;
        for(int s = 0; s < 1 << n; ++s) {
            LL va = 0, vb = 0;
            for(int i = 0; i < n; ++i) {
                if(s >> i & 1) {
                    va += v[hf + i];
                    vb += 1LL * v[hf + i] * v[hf + i];
                }
            }
            ans += KDT::query(root, {va, va * va - vb});
        }
        printf("%d\n", ans - 1);
    }

    return 0;
}

```