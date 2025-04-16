# 并查集

复杂度：

预处理：$O(n)$

查询： $O(logn)$

## 模板a

```cpp
struct DSU {
    vector<int> f, siz;
    DSU(int n) : f(n), siz(n, 1) { iota(f.begin(), f.end(), 0); }

    int leader(int x) {
        while (x != f[x]) x = f[x] = f[f[x]];
        return x;
    }

    bool same(int x, int y) { return leader(x) == leader(y); }

    bool merge(int x, int y) {
        x = leader(x);
        y = leader(y);
        if (x == y) return false;
        siz[x] += siz[y];
        f[y] = x;
        return true;
    }

    int size(int x) { return siz[leader(x)]; }
};
```

[Unionfind](https://judge.yosupo.jp/problem/unionfind)

## 用法

```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q;
    DSU dsu(n);

    while (q--) {
        int o, x, y;
        cin >> o >> x >> y;
        if (not o) {
            dsu.merge(x, y);
        } else {
            cout << dsu.same(x, y) << '\n';
        }
    }

    return 0;
}
```



## 模板b

防卡常

```c++
int cnt[N], p[N];

int find(int x) {
    if (p[x] == x) return x;
    return p[x] = find(p[x]);
}

bool merge(int u, int v) {
    u = find(u), v = find(v);
    if (u == v) return false;
    cnt[u] += cnt[v];
    p[v] = u;
    return true;
}

for (int i = 1; i <= n; i++) {
    p[i] = i;
    cnt[i] = 1;
}
```



## 模板c

种类并查集

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

struct Info {
    int x, y, z;
};

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, m;
    cin >> n >> m;
    vector<int> p(n * 2 + 1);
    iota(p.begin(), p.end(), 0);

    vector<Info> adj(m);
    for (int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        adj[i] = {u, v, w};
    }
    sort(adj.begin(), adj.end(), [&](Info lth, Info rth) { return lth.z > rth.z; });

    function<int(int)> find = [&](int x) {
        if (p[x] == x) return x;
        return p[x] = find(p[x]);
    };

    int ans = 0;
    for (int i = 0; i < m; i++) {
        auto [u, v, w] = adj[i];
        if (find(u) == find(v) or find(u + n) == find(v + n)) {
            ans = w;
            break;
        }
        p[p[u + n]] = p[v];
        p[p[u]] = p[v + n];
    }
    cout << ans << '\n';

    return 0;
}
```

和普通并查集类似,但是开两倍的点集
然后如果是同一阵营,就把u和v合并,把u+n和v+n合并
否则就把u+n和v合并,把u和v+n合并
