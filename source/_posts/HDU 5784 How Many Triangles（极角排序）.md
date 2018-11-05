---
title: HDU 5784 How Many Triangles（极角排序）
categories:
  - 计算几何
  - 极角排序
  - 
tags:
  - 极角排序
  - 
  - 
date: 2016-08-04 09:30:10
toc: 
---

题意：
>$给定N\le 2000个二维平面不重点，求形成的锐角三角形的个数$

<!-- more -->
分析：
>$总而言之，这种就是极角排序之后然后two\ pointers$
$由于是个环倍增一下，无论你atan2搞还是点积叉积搞，思路都是一样的$
$来看看这题的两种计算姿势，一种是题解的比较tricky一点：$
$锐角三角形={cnt_{锐角}-2\times(cnt_{直角、钝角})\over 3}，注意这里是角$
$至于怎么算的，看贡献，锐角三角形贡献3个锐角，直角、钝角三角形贡献2个锐角$
$这个统计方法也比较simple，two\ pointers先统计锐角、然后再统计锐、直、钝一起$
$一减就得到了想要的2个东西$
$对于第二种做法，比较general一点：$
$锐角三角形=总三角形数-直角、钝角、平角三角形数，注意这里是三角形$
$two\ pointers统计的时候直接把锐角和0°一起统计了$
$直角、钝角、平角三角形数=C(n-1,\ 2)-统计出来的$
$最后再用C(n,\ 3)减一下就好了$
$两者时间复杂度都是O(n^2logn)$
$注意atan2的精度，毕竟atan2(2e9,\ 1)在$ `1e-10` $的量级，多取2个量级取$ `1e-12` 就好


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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double EPS = 1e-12, PI = acos(-1);

int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

int n;
struct Point {
    int x, y;
    double ang;
    void read() {scanf("%d%d", &x, &y);}
} p[N];

int calc(vector<Point>& v, double delta) {
    int angle = 0;
    for(int i = 0, j = 0, k = 0; i < n - 1; i = k + 1) {
        //collinear
        while(k + 1 < n - 1 && sgn(v[k + 1].ang - v[i].ang) == 0) ++k;
        j = max(j, k);
        while(j < v.size() && sgn(v[j].ang - v[i].ang - delta) < 0) ++j;
        angle += (k - i + 1) * (j - k - 1);
    }
    return angle;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 1; i <= n; ++i) p[i].read();

        int acute = 0, obtuse = 0;
        for(int i = 1; i <= n; ++i) {
            vector<Point> v;
            for(int j = 1; j <= n; ++j) if(j != i) v.push_back(p[j]);
            for(int j = 0; j < n - 1; ++j)
                v[j].ang = atan2(v[j].y - p[i].y, v[j].x - p[i].x);
            sort(v.begin(), v.end(), [&](Point x, Point y) {
                return x.ang < y.ang;
            });

            //double it
            for(int j = 0; j < n - 1; ++j){
                Point tmp = v[j];
                tmp.ang += 2 * PI;
                v.push_back(tmp);
            }

            int curAcute = calc(v, PI / 2);
            int tot = calc(v, PI);

            acute += curAcute;
            obtuse += tot - curAcute;
        }
        int ans = (acute - 2 * obtuse) / 3;
        printf("%d\n", ans);
    }
    return 0;
}
```

代码二：
(来自mathon)
```cpp
/************************************************
 *Author        :mathon
 *Email         :luoxinchen96@gmail.com
*************************************************/
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <vector>
#include <queue>
#include <set>
#include <map>
#include <string>
#include <cmath>
#include <cstdlib>
#include <ctime>
#include <stack>
using namespace std;
typedef pair<int, int> pii;
typedef long long ll;
typedef unsigned long long ull;
#define xx first
#define yy second
#define pr(x) cout << #x << " " << x << " "
#define prln(x) cout << #x << " " << x << endl
template<class T> inline T lowbit(T x) { return x & (-x); }
const int MAXN = 2000 + 5;

struct Point {
    ll x, y;
    Point() {}
    Point(ll x, ll y): x(x), y(y) {}
    void read() {
        scanf("%lld%lld", &x, &y);
    }
    Point operator - (const Point& b) const {
        return Point(x - b.x, y - b.y);
    }
    ll cross(const Point& b) const {
        return x * b.y - y * b.x;
    }
    ll dot(const Point& b) const {
        return x * b.x + y * b.y;
    }
    void print() {
        printf("x = %lld, y = %lld\n", x, y);
    }
} ps[MAXN];
int n;

bool cmp(const Point& a, const Point& b) {
    if(a.y * b.y <= 0) {
        if(a.y > 0 || b.y > 0) return a.y < b.y;
        if(a.y == 0 && b.y == 0) return a.x < b.x;
    }
    return a.cross(b) > 0;
}

Point buf[MAXN * 2];

int main(void) {
#ifdef MATHON
    freopen("1004.in", "r", stdin);
    freopen("out.txt", "w", stdout);
#endif
    while(scanf("%d", &n) == 1) {
        for(int i = 0; i < n; i++) {
            ps[i].read();
        }
        ll ans = 0;
        for(int k = 0; k < n; k++) {
            int cnt = 0;
            for(int j = 0; j < n; j++) {
                if(k == j) continue;
                buf[cnt++] = ps[j] - ps[k];
            }
            sort(buf, buf + cnt, cmp);
            memcpy(buf + cnt, buf, sizeof(Point) * cnt);
            ll tmp = 0;
            for(int i = 0, j = 0; i < cnt; i++) {
                if(i == j) while(j < i + cnt && buf[i].cross(buf[j]) == 0
                                     && buf[i].dot(buf[j]) > 0) j++;
                while(j < i + cnt && buf[i].cross(buf[j]) > 0 && buf[i].dot(buf[j]) > 0) j++;
                tmp += j - i - 1;
                // pr(i); prln(j);
            }
            // prln(tmp);
            tmp = (cnt) * (cnt - 1) / 2  - tmp;
            ans += tmp;
        }
        ans = ll(n) * (n - 1) * (n - 2) / 6 - ans;
        cout << ans << endl;
    }
    return 0;
}

```