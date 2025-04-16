# 第k小/第k大

给你一个集合，静态或者动态维护第k小或第k大

## 对顶堆

复杂度：

预处理： $O(nlogn)$

单次查询： $O(logn)$

[luogu P1138 第k小整数](https://www.luogu.com.cn/problem/P1138)

有 $n$ 个正整数，要求出这 $n$ 个正整数中的第 $k$ 个最小整数（相同大小的整数只计算一次）。输出第 $k$ 个最小整数的值；若无解，则输出 `NO RESULT`。

## 代码

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, k;
    cin >> n >> k;
    priority_queue<int> big;
    priority_queue<int, vector<int>, greater<int>> small;
    set<int> st;

    for (int i = 1, x; i <= n; i++) {
        cin >> x;
        if (st.find(x) != st.end()) {
            continue;
        }
        st.insert(x);
        if (small.empty() or x >= small.top()) {
            small.emplace(x);
        } else {
            big.emplace(x);
        }
    }

    auto kth_Max = [&](int k) {
        while (small.size() > k) {
            big.emplace(small.top());
            small.pop();
        }
        while (small.size() < k and not big.empty()) {
            small.emplace(big.top());
            big.pop();
        }
        if (small.size() != k) {
            return numeric_limits<int>::max();
        }
        return small.top();
    };

    auto kth_Min = [&](int k) {
        while (big.size() > k) {
            small.emplace(big.top());
            big.pop();
        }
        while (big.size() < k and not small.empty()) {
            big.emplace(small.top());
            small.pop();
        }
        if (big.size() != k) {
            return numeric_limits<int>::max();
        }
        return big.top();
    };

    auto ans = kth_Min(k);
    if (ans == numeric_limits<int>::max()) {
        cout << "NO RESULT" << '\n';
    } else {
        cout << ans << '\n';
    }

    return 0;
}
```

