# 单调队列

## 模板

```c++
auto getmn = [&](vector<int>& a, int k) {
    deque<int> q;
    vector<int> mn;
    for (int i = 1; i <= n; i++) {
        while (not q.empty() and a[q.back()] >= a[i]) {
            q.pop_back();
        }
        q.push_back(i);
        if (q.front() <= i - k) {
            q.pop_front();
        }
        if (i >= k) {
            mn.emplace_back(a[q.front()]);
        }
    }
    return mn;
};

auto getmx = [&](vector<int>& a, int k) {
    deque<int> q;
    vector<int> mx;
    for (int i = 1; i <= n; i++) {
        while (not q.empty() and a[q.back()] <= a[i]) {
            q.pop_back();
        }
        q.push_back(i);
        if (q.front() <= i - k) {
            q.pop_front();
        }
        if (i >= k) {
            mx.emplace_back(a[q.front()]);
        }
    }
    return mx;
};
```



[P1886 滑动窗口 /【模板】单调队列](https://www.luogu.com.cn/problem/P1886)

有一个长为 $n$ 的序列 $a$ ，以及一个大小为 $k$ 的窗口。现在这个从左边开始向右滑动，每次滑动一个单位，求出每次滑动后窗口中的最大值和最小值。

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
    vector<int> a(n + 1);
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }

    auto getmn = [&](vector<int>& a, int k) {
        deque<int> q;
        vector<int> mn;
        for (int i = 1; i <= n; i++) {
            while (not q.empty() and a[q.back()] >= a[i]) {
                q.pop_back();
            }
            q.push_back(i);
            if (q.front() <= i - k) {
                q.pop_front();
            }
            if (i >= k) {
                mn.emplace_back(a[q.front()]);
            }
        }
        return mn;
    };

    auto getmx = [&](vector<int>& a, int k) {
        deque<int> q;
        vector<int> mx;
        for (int i = 1; i <= n; i++) {
            while (not q.empty() and a[q.back()] <= a[i]) {
                q.pop_back();
            }
            q.push_back(i);
            if (q.front() <= i - k) {
                q.pop_front();
            }
            if (i >= k) {
                mx.emplace_back(a[q.front()]);
            }
        }
        return mx;
    };

    auto mn = getmn(a, k), mx = getmx(a, k);
    for (int i = 0; i < mn.size(); i++) {
        cout << mn[i] << " \n"[i == mn.size() - 1];
    }
    for (int i = 0; i < mx.size(); i++) {
        cout << mx[i] << " \n"[i == mx.size() - 1];
    }

    return 0;
}
```

