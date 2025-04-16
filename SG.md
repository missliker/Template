# 定理

必胜态的后继状态中至少存在一种必败态

必败态的后继状态均为必胜态

# nim游戏

$sg(x)=x$

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

void Solution(int T) {
    int n;
    cin >> n;
    int ans = 0;
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
        ans ^= x;
    }
    cout << (ans ? "Yes" : "No") << '\n';
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int MAXT = 1;
    cin >> MAXT;
    for (int T = 1; T <= MAXT; T++) Solution(T);

    return 0;
}
```



# 台阶型nim

[P10507 Georgia and Bob](https://www.luogu.com.cn/problem/P10507)

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

void Solution(int T) {
    int n;
    cin >> n;

    vector<int> a(n + 1), b;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    sort(a.begin() + 1, a.end());
    for (int i = n; i >= 1; i--) {
        b.emplace_back(a[i] - a[i - 1] - 1);
    }

    int ans = 0;
    for (int i = 0; i < b.size(); i += 2) {
        ans ^= b[i];
    }
    debug(ans);
    cout << (ans ? "Georgia will win" : "Bob will win") << '\n';
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int MAXT = 1;
    cin >> MAXT;
    for (int T = 1; T <= MAXT; T++) Solution(T);

    return 0;
}
```



# nim2

每次只准拿[l, r]个,没法拿的人输,A先手,A必赢输出First

$sg(x)=(x\%(l+r))/l $

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

    int n, l, r;
    cin >> n >> l >> r;
    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // vector<int> f(100000, -1);
    // auto sg = [&](auto self, int x) -> int {
    //     if (f[x] != -1) {
    //         return f[x];
    //     }

    //     set<int> s;
    //     for (int i = max(0, x - r); i <= min(0, x - l); i++) {
    //         s.insert(self(self, i));
    //     }

    //     for (int i = 0;; i++) {
    //         if (not s.count(i)) {
    //             return f[x] = i;
    //         }
    //     }
    // };

    // int ans = 0;
    // for (int i = 0; i < n; i++) {
    //     ans ^= sg(sg, a[i]);
    // }

    int ans = 0;

    auto sg = [&](int x) {
        return (x % (l + r)) / l;
    };

    for (int i = 0; i < n; i++) {
        ans ^= sg(a[i]);
    }
    cout << (ans ? "First" : "Second") << '\n';

    return 0;
}
```





# 有向图游戏

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

    int n, m, k;
    cin >> n >> m >> k;

    vector adj(n + 1, vector<int>());
    for (int i = 1; i <= m; i++) {
        int u, v;
        cin >> u >> v;
        adj[u].emplace_back(v);
    }

    vector<int> f(n + 1, -1);

    auto sg = [&](auto self, int u) -> int {
        if (f[u] != -1) {
            return f[u];
        }
        set<int> s;
        for (auto v : adj[u]) {
            s.insert(self(self, v));
        }
        for (int i = 0;; i++) {
            if (not s.count(i)) {
                return f[u] = i;
            }
        }
    };

    int ans = 0;
    for (int i = 1; i <= k; i++) {
        int x;
        cin >> x;
        ans ^= sg(sg, x);
    }
    cout << (ans ? "win" : "lose") << '\n';

    return 0;
}
```



# 剪纸游戏

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

const int N = 210;
int f[N][N];

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, m;
    memset(f, -1, sizeof f);
    while (cin >> n >> m and n) {
        auto sg = [&](auto self, int a, int b) -> int {
            if (f[a][b] != -1) {
                return f[a][b];
            }

            set<int> s;
            for (int i = 2; i <= a - 2; i++) {
                s.insert(self(self, i, b) ^ self(self, a - i, b));
            }
            for (int i = 2; i <= b - 2; i++) {
                s.insert(self(self, a, i) ^ self(self, a, b - i));
            }

            for (int i = 0;; i++) {
                if (not s.count(i)) return f[a][b] = f[b][a] = i;
            }
        };

        cout << (sg(sg, n, m) ? "WIN" : "LOSE") << '\n';
    }

    return 0;
}
```

