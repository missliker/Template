# 单源最短路

## Dijkstra

```cpp
const i64 INF = 1E18;
auto dij = [&](int n, vector<int>& start) {
    vector<i64> dist(n + 1, INF);
    vector<int> st(n + 1);
    for (auto& i : start) {
        dist[i] = 0;
    }

    for (int i = 0; i < n - 1; i++) {
        int t = -1;
        for (int j = 1; j <= n; j++)
            if (not st[j] and (t == -1 or dist[t] > dist[j])) {
                t = j;
            }

        for (int j = 1; j <= n; j++) {
            dist[j] = min(dist[j], dist[t] + g[t][j]);
        }

        st[t] = true;
    }
    return dist;
};
auto dis = dij(n, start);
```

### 具体使用

```cpp
vector<vector<i64>> g(n + 1, vector<i64>(n + 1, INF));

g[u][v] = min(g[u][v], w);
g[v][u] = min(g[v][u], w);
```



## Dijkstra(堆优，链前版)

```cpp
const i64 INF = 1E18;
using Pair = pair<i64, i64>;
auto dij = [&](int n, vector<int>& start) {
    vector<i64> dist(n + 1, INF);
    vector<int> st(n + 1);
    priority_queue<Pair, vector<Pair>, greater<Pair>> heap;
    for (auto& i : start) {
        dist[i] = 0;
        heap.emplace(0, i);
    }
    while (not heap.empty()) {
        auto [dis, u] = heap.top();
        heap.pop();
        if (st[u]) {
            continue;
        }
        st[u] = true;

        for (int i = head[u]; i; i = adj[i].nxt) {
            int v = adj[i].to;
            i64 w = adj[i].w;
            if (dist[v] > dis + w) {
                dist[v] = dis + w;
                heap.emplace(dist[v], v);
            }
        }
    }

    return dist;
};
auto dis = dij(n, start);
```

### 具体使用

```cpp
constexpr int N = 5E6 + 10;
struct {
    int to, nxt;
    i64 w;
} adj[N];

int tot;
int head[N];

inline void add(int u, int v, i64 w) {
    adj[++tot].nxt = head[u];
    adj[tot].to = v;
    adj[tot].w = w;
    head[u] = tot;
}

// for (int i = head[u]; i; i = adj[i].nxt) {
//     int v = adj[i].to;
//     i64 w = adj[i].w;
// }

add(u, v, w), add(v, u, w);
```



## Dijkstra(堆优，vector版)

```cpp
const i64 INF = 1E18;
using Pair = pair<i64, i64>;
auto dij = [&](int n, vector<int>& start) {
    vector<i64> dist(n + 1, INF);
    vector<int> st(n + 1);
    priority_queue<Pair, vector<Pair>, greater<Pair>> heap;
    for (auto& i : start) {
        dist[i] = 0;
        heap.emplace(0, i);
    }
    
    while (not heap.empty()) {
        auto [dis, u] = heap.top();
        heap.pop();
        if (st[u]) {
            continue;
        }
        st[u] = true;

        for (auto [v, w] : adj[u]) {
            if (dist[v] > dis + w) {
                dist[v] = dis + w;
                heap.empalce(dist[v], v);
            }
        }
    }

    return dist;
};
auto dis = dij(n, start);
```

### 具体使用

```cpp
vector<vector<Pair>> adj(n + 1);

adj[u].emplace_back(v, w);
adj[v].emplace_back(u, w);
```



## SPFA(链前版)

```cpp
const i64 INF = 1E18;
auto spfa = [&](int n, vector<int>& start) {
    vector<i64> dist(n + 1, INF);
    vector<int> st(n + 1);
    queue<int> q;
    for (auto& i : start) {
        dist[i] = 0;
        q.emplace(i);
        st[i] = true;
    }

    while (not q.empty()) {
        auto u = q.front();
        q.pop();
        st[u] = false;

        for (int i = head[u]; i ; i = adj[i].nxt) {
            int v = adj[i].to;
            i64 w = adj[i].w;
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                if (st[v]) {
                    continue;
                }
                q.emplace(v);
                st[v] = true;
            }
        }
    }

    return dist;
};
auto dis = spfa(n, start);
```

### 具体用法

```cpp
constexpr int N = 5E6 + 10;
struct {
    int to, nxt;
    i64 w;
} adj[N];

int tot;
int head[N];

inline void add(int u, int v, i64 w) {
    adj[++tot].nxt = head[u];
    adj[tot].to = v;
    adj[tot].w = w;
    head[u] = tot;
}

// for (int i = head[u]; i; i = adj[i].nxt) {
//     int v = adj[i].to;
//     i64 w = adj[i].w;
// }

add(u, v, w), add(v, u, w);
```



## SPFA(vector版)

```cpp
const i64 INF = 1E18;
auto spfa = [&](int n, vector<int>& start) {
    vector<i64> dist(n + 1, INF);
    vector<int> st(n + 1);
    queue<int> q;
    for (auto& i : start) {
        dist[i] = 0;
        q.emplace(i);
        st[i] = true;
    }

    while (not q.empty()) {
        auto u = q.front();
        q.pop();
        st[u] = false;

        for (auto [v, w] : adj[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                if (st[v]) {
                    continue;
                }
                q.emplace(v);
                st[v] = true;
            }
        }

    }

    return dist;
};
auto dis = spfa(n, start);
```

### 具体使用

```cpp
vector<vector<Pair>> adj(n + 1);

adj[u].emplace_back(v, w);
adj[v].emplace_back(u, w);
```



# 多源最短路

## Floyd

```c++
const i64 INF = 1E18;
vector dis(n + 1, vector<i64>(n + 1, INF));
for (int i = 0; i <= n; i++) {
    dis[i][i] = 0;
}

for (int i = 1; i <= m; i++) {
    i64 u, v, w;
    cin >> u >> v >> w;
    dis[u][v] = min(dis[u][v], w);
    dis[v][u] = min(dis[v][u], w);
}

for (int k = 1; k <= n; k++) {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
        }
    }
}
```



