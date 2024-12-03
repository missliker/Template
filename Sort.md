# 排序

## MergeSort

```c++
auto nxd = [&](vector<int>& v) {
    i64 cnt = 0;
    int n = v.size();
    vector<int> tmp(n + 1);

    function<void(int, int)> mergeSort = [&](int l, int r) {
        if (l >= r) return;
        int mid = (l + r) / 2;
        mergeSort(l, mid), mergeSort(mid + 1, r);

        int k = 0, i = l, j = mid + 1;
        while (i <= mid and j <= r) {
            if (v[i] <= v[j]) {
                tmp[k++] = v[i++];
            } else {
                tmp[k++] = v[j++];
                cnt += mid - i + 1;
            }
        }
        while (i <= mid) {
            tmp[k++] = v[i++];
        }
        while (j <= r) {
            tmp[k++] = v[j++];
        }

        for (i = l, j = 0; i <= r; i++, j++) {
            v[i] = tmp[j];
        }
    };
    mergeSort(1, n);
    return cnt;
};
```



## TopoSort

```c++
vector<vector<int>> adj(n + 1);
vector<int> in(n + 1);
auto topoSort = [&]() {
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

