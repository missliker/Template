# TopoSort

```c++
vector<vector<int>> adj;
vector<int> in(n + 1);
auto toposort = [&]() {
    vector<int> L;
    queue<int> q;
    for (int i = 1; i <= n; i++) {
        if (in[i] == 0) {
            q.emplace(i);
        }
    }
    while (not q.empty()) {
        int u = q.front();
        q.pop();
        L.emplace_back(u);
        for (auto v : adj[u]) {
            if (--in[v] == 0) {
                q.emplace(v);
            }
        }
    }
    if (L.size() == n) {
        for (auto i : L) {
            cout << i << ' ';
        }
        return true;
    }
    return false;
};
```

