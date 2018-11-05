---
title: HDU 5297 Y sequence (容斥、迭代)
categories:
  - 数学
  - 容斥
  - 
tags:
  - 容斥
  - 迭代
date: 2016-04-28 01:33:10
toc: 
---
题意：
>$Y序列：不包含形如a^b(2\le b\le r,\ 2\le r\le 62)的数，并且Y(1)=2$
$求给定r下的Y(n)，N\le 2\times 10^{18}$

<!-- more -->

分析：
>$这个题类似于之前做过的容斥题$[HDU 2204 Eddy's爱好](http://acm.hdu.edu.cn/showproblem.php?pid=2204)
$这种题的关键是如何不重不漏的计数，显然4^2会在2^4重复计数，所以我们就记最小的那个$
$接下来的关键就是容斥了，只容斥幂的素因子就好了，打好62内的素数表，注意选定的素因子不能超过r$
$2\times 3\times 5\times 7>62，所以容斥的素因子个数不会超过3，复杂度不是很高$
$但是容斥的时候是可以的，但是不能超过62，因为4^{31}=2^{62}>10^{18}$
$还要指数不能是1啊，然后统计n以后有多少a^e的数，直接把n开e次方就好了$
$据说会炸精度，上long\ double能存18位比较好$
$二分确实会T，我试过了，然后只要迭代就可以了，每次就加缺少的个数。。学到了$
$然后就是O(跑得过)了$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-27
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;

LL n, r;
vector<int> prime = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37,
                     41, 43, 47, 53, 59, 61
                    };

void dfs(int p, int e, int cnt, LL n, LL& sum) {
    if(e > 62) return; //4^31 -> 2^62
    if(p == prime.size()) {
        if(e == 1) return;
        LL cur = pow((long double)(n + 0.5), 1.0 / e) - 1;
        if(cnt & 1) sum += cur;
        else sum -= cur;
        return;
    }
    dfs(p + 1, e, cnt, n, sum);
    if(prime[p] <= r) dfs(p + 1, e * prime[p], cnt + 1, n, sum);
}

LL calc(LL n) {
    LL sum = 0;
    dfs(0, 1, 0, n, sum);
    return n - sum - 1;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%I64d%I64d", &n, &r);
        LL ans = n, cnt = calc(n);
        while(cnt < n) {
            ans += n - cnt;
            cnt = calc(ans);
        }
        printf("%I64d\n", ans);
    }
    return 0;
}
```