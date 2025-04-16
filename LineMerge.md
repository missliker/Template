# 区间合并

对于多条线段进行合并

```c++
vector<Pair> merge(vector<Pair>& segs) {
    vector<Pair> res;
    ranges::sort(segs);
    for (auto& p : segs) {
    	if (not res.empty() and p.first <= res.back().second) {
    		res.back().second = max(res.back().second, p.second);
    	} else {
    		res.emplace_back(p);
    	}
    }
    return res;
}
```

```c++
vector<Pair> merge(vector<Pair>& segs) {
    vector<Pair> res;
    ranges::sort(segs);
    decltype(segs.begin()->first) st = -2e9, ed = -2e9;
    for (auto& seg : segs) {
        if (ed < seg.first) {
            if (st != -2e9) {
                res.emplace_back(st, ed);
            }
            st = seg.first, ed = seg.second;
        } else {
            ed = max(ed, seg.second);
        }
    }
    if (st != -2e9) {
        res.emplace_back(st, ed);
    }
    return res;
}
```



## 例题

[火烧赤壁](https://www.luogu.com.cn/problem/P1496)

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using Pair = pair<int, int>;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

vector<Pair> merge(vector<Pair>& segs) {
    vector<Pair> res;
    ranges::sort(segs);
    for (auto& p : segs) {
        if (not res.empty() and p.first <= res.back().second) {
            res.back().second = max(res.back().second, p.second);
        } else {
            res.emplace_back(p);
        }
    }
    return res;
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n;
    cin >> n;
    vector<Pair> segs(n);
    for (auto& [l, r] : segs) {
        cin >> l >> r;
    }

    segs = merge(segs);
    i64 ans = 0;
    for (auto& [l, r] : segs) {
        ans += r - l;
    }
    cout << ans << '\n';

    return 0;
}
```

