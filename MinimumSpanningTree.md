# 最小生成树

有n个点,m条边,判断是否生成最小生成树,如果能生成,则输出最小生成树的树边权重之和,否则输出-1

主流有两种算法，Prim和Kruskal

## Prim

复杂度： $O(nlogn + m)$

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using Pair = pair<i64, i64>;
constexpr i64 INF = 1E18 + 10;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

struct Info {
    int u;
    i64 d;
    Info() { u = 0, d = 0; }
    Info(int u, i64 d) : u(u), d(d) {}
};
bool operator<(const Info& lth, const Info& rth) { return lth.d > rth.d; }

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, m;
    cin >> n >> m;
    vector adj(n + 1, vector<Pair>());
    for (int i = 1; i <= m; i++) {
        i64 u, v, w;
        cin >> u >> v >> w;
        adj[u].emplace_back(v, w);
        adj[v].emplace_back(u, w);
    }

    i64 res = 0, cnt = 0;
    priority_queue<Info> heap;
    heap.emplace(1, 0);

    vector<i64> dis(n + 1, INF);
    vector<int> st(n + 1);
    dis[1] = 0;

    while (not heap.empty()) {
        if (cnt >= n) {
            break;
        }
        auto [u, d] = heap.top();
        heap.pop();
        if (st[u]) {
            continue;
        }
        st[u] = 1;
        cnt++;
        res += d;
        for (auto& [v, w] : adj[u]) {
            if (w < dis[v]) {
                dis[v] = w;
                heap.emplace(v, w);
            }
        }
    }
    if (cnt != n) {
        res = -1;
    }
    cout << res << '\n';

    return 0;
}
```

## Kruskal

复杂度： $O(mlogm)$

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using Pair = pair<i64, i64>;
constexpr i64 INF = 1E18 + 10;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

struct Info {
    int u, v;
    i64 w;
    bool operator<(const Info& t) const { return w < t.w; }
};

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, m;
    cin >> n >> m;
    vector<Info> adj;
    for (int i = 1; i <= m; i++) {
        i64 u, v, w;
        cin >> u >> v >> w;
        adj.emplace_back(u, v, w);
    }
    sort(adj.begin(), adj.end());

    vector<int> p(n + 1);
    iota(p.begin(), p.end(), 0);

    function<int(int)> find = [&](int x) {
        return p[x] = (p[x] == x ? x : find(p[x]));
    };

    i64 res = 0, cnt = 0;
    for (auto [u, v, w] : adj) {
        u = find(u), v = find(v);
        if (u != v) {
            res += w;
            cnt++;
            p[u] = v;
        }
    }
    if (cnt != n - 1) {
        res = -1;
    }
    cout << res << '\n';

    return 0;
}
```

