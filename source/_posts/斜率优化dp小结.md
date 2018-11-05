---
title: 斜率优化dp小结
categories:
  - 小结
  - 
  - 
tags:
  - 斜率优化
  - 
date: 2016-05-11 00:52:10
toc: true
---

## Ⅰ. 斜率优化认知
* 这东西英文名叫$Convex\ Hull\ Trick\ (CHT)$
* 主要优化形如$f_i = min\{\ f_j + cost(j+1,\ i)\ \}的这种dp$
* 由于等号右边的式子并不能$O(1)$计算，所以我们需要一些技巧来优化

<!-- more -->

## Ⅱ. 斜率优化
* $懒得写引子了，就直接推推推吧$
* $对于当前状态i，有2个决策j和k，不妨假设k < j < i，如果决策j优于k，则有：$
$$f_j + cost(j+1,\ i)<f_k + cost(k+1,\ i)$$
* $如果以上这个式子可以化简到：$
$$ \frac{Y(j)-Y(k)}{X(j)-X(k)}<F(i) $$
* $记这个式子为slope(k,\ j)=\frac{Y(j)-Y(k)}{X(j)-X(k)}<F(i)，即j优于k$
* $便可以使用优化，这个式子很斜率，故称这个优化方法为斜率优化$

---
* $我们发现以上并没有什么卵用，先说一个结论$
* $结论：如果slope(k,\ j)\ge slope(j,\ i)，k < j < i，那么j决策是不优的可以删除$
* $证明：$
* $如果slope(j,\ i) < F(i)，那么i优于j，j可以删除$
* $如果slope(j,\ i)\ge F(i)，虽然j优于i，但是slope(k,\ j)\ge F(i)，k比j优，j还是可以删除\ \ \ \blacksquare$
* $然后就可以用这个结论来维护一个单调队列来搞了$

---

## Ⅲ. 题目讲解

### HDU 3507 Print Article
分析:
$f_i:=打印前i个字符的最小代价$
$f_i = min\{\ f_j + (sum_i - sum_{j})^2 + M\ \}，sum_i=\sum_{i=1}^n c_i$
$假设k<j<i，假设j优于k，可得：$
$$ f_j + (sum_i - sum_{j})^2 + M < f_k + (sum_i - sum_{k})^2 + M $$
$$ f_j +sum_i^2-2\cdot sum_i \cdot sum_j + sum_j^2 < f_k +sum_i^2-2\cdot sum_i \cdot sum_k + sum_k^2 $$
$$ (f_j + sum_j^2)-(f_k+sum_k^2) < 2\cdot sum_i\cdot (sum_j-sum_k) $$
$$ \frac{(f_j + sum_j^2)-(f_k+sum_k^2)}{sum_j-sum_k} < sum_i $$
$即j优于k的条件是：slope(k,\ j)=\frac{(f_j + sum_j^2)-(f_k+sum_k^2)}{sum_j-sum_k} < sum_i $


* $维护单调队列，开区间[L,\ R)：$
* $先删除队头不优的元素，即slope(q[L],\ q[L+1])\le sum_i$
* $此时q[L+1]不差于q[L]，所以q[L]可以删除$
* $用最优的j=q[L]更新f_i$
* $再用f_i去删除队尾的不优元素，即slope(q[R-2],\ q[R-1])\ge slope(q[R-1],\ i)$
* $时间复杂度O(n)$

参考代码：

```cpp
//
//  Created by TaoSama on 2016-05-10
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
const int N = 5e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int s[N], q[N];
int f[N];

int up(int k, int j) {
    return (f[j] + s[j] * s[j]) - (f[k] + s[k] * s[k]);
}

int dw(int k, int j) {
    return 2 * (s[j] - s[k]);
}

bool check(int k, int j, int i) {
    return up(k, j) * dw(j, i) >= up(j, i) * dw(k, j);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d%d", &n, &m) == 2) {
        for(int i = 1; i <= n; ++i) {
            scanf("%d", s + i);
            s[i] += s[i - 1];
        }

        int L = 0, R = 0;
        q[R++] = f[0] = 0;
        for(int i = 1; i <= n; ++i) {
            while(L + 1 < R && up(q[L], q[L + 1]) <= s[i] * dw(q[L], q[L + 1])) ++L;
            int j = q[L];
            f[i] = f[j] + m + (s[i] - s[j]) * (s[i] - s[j]);
            while(L + 1 < R && check(q[R - 2], q[R - 1], i)) --R;
            q[R++] = i;
        }

        printf("%d\n", f[n]);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}

```

