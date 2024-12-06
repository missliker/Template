# 值域桶

例：给你一个数组 $a$ ，找每个下标 $i$ 左边第一次出现 $a[i]$ 的位置和右边第 $k-1$ 次出现 $a[i]$ 的位置。

若想维护右边第一次出现 $a[i]$ 的位置，直接把 $k$ 设置为 $2$ 即可

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
    vector id(n + 1, vector<int>(1, 0));
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
        id[a[i]].emplace_back(i);
    }

    debug(id);

    vector<int> lst(n + 1), nxt(n + 1, -1);
    for (int i = 1; i <= n; i++) {
        for (int l = 1, r = k; l < id[i].size(); l++, r++) {
            lst[id[i][l]] = id[i][l - 1];
            if (r < id[i].size()) {
                nxt[id[i][l]] = id[i][r];
            }
        }
    }
    debug(lst);
    debug(nxt);

    return 0;
}
```

## 样例1

```c++
13 2
1 1 4 5 1 4 1 9 1 9 8 4 5

D:\C++\main.cpp:24: (id) = ([[0], [0, 1, 2, 5, 7, 9], [0], [0], [0, 3, 6, 12], [0, 4, 13], [0], [0], [0, 11], [0, 8, 10], [0], [0], [0], [0]])
D:\C++\main.cpp:33: (lst) = ([0, 0, 1, 0, 0, 2, 3, 5, 0, 7, 8, 0, 6, 4])
D:\C++\main.cpp:34: (nxt) = ([-1, 2, 5, 6, 13, 7, 12, 9, 10, -1, -1, -1, -1, -1])
```

## 样例2

```c++
13 3
1 1 4 5 1 4 1 9 1 9 8 4 5

D:\C++\main.cpp:24: (id) = ([[0], [0, 1, 2, 5, 7, 9], [0], [0], [0, 3, 6, 12], [0, 4, 13], [0], [0], [0, 11], [0, 8, 10], [0], [0], [0], [0]])
D:\C++\main.cpp:33: (lst) = ([0, 0, 1, 0, 0, 2, 3, 5, 0, 7, 8, 0, 6, 4])
D:\C++\main.cpp:34: (nxt) = ([-1, 5, 7, 12, -1, 9, -1, -1, -1, -1, -1, -1, -1, -1])
```

