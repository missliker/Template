# 排序
## quick sort
```cpp
void quickSort(vector<int>& a, int l, int r) {
    if (l >= r) return;
    int i = l, j = r, pivot = a[l];
    while (i < j) {
        while (i < j and a[j] >= pivot) j--;
        while (i < j and a[i] <= pivot) i++;
        swap(a[i], a[j]);
    }
    swap(a[l], a[i]);
    quickSort(a, l, i - 1);
    quickSort(a, i + 1, r);
}
```

## merge sort
```cpp
void mergeSort(vector<int>& a, int l, int r) {
    if (l >= r) return;
    int mid = (l + r) / 2;
    mergeSort(a, l, mid);
    mergeSort(a, mid + 1, r);
    vector<int> temp;
    int i = l, j = mid + 1;
    while (i <= mid and j <= r) 
        temp.push_back(a[i] < a[j] ? a[i++] : a[j++]);
    while (i <= mid) temp.push_back(a[i++]);
    while (j <= r) temp.push_back(a[j++]);
    for (int k = 0; k < temp.size(); k++) 
        a[l + k] = temp[k];
}
```

```c++
auto mergeSort = [&](vector<int>& in) {
    auto vec = in;
    int n = int(vec.size()) - 1;
    assert(n >= 1);
    vector<int> tmp(n + 1);

    auto mSort = [&](this auto&& self, int l, int r) {
        if (l >= r) return;
        int m = (l + r) / 2;
        self(l, m), self(m + 1, r);

        int k = 0, i = l, j = m + 1;
        while (i <= m and j <= r) {
            if (vec[i] <= vec[j]) {
                tmp[k++] = vec[i++];
            } else {
                tmp[k++] = vec[j++];
            }
        }
        while (i <= m) {
            tmp[k++] = vec[i++];
        }
        while (j <= r) {
            tmp[k++] = vec[j++];
        }

        for (i = l, j = 0; i <= r; i++, j++) {
            vec[i] = tmp[j];
        }
    };
    mSort(1, n);

    return vec;
};
```

### 求逆序对

```c++
auto getInvPair = [&](vector<int>& in) {
    auto vec = in;
    int n = int(vec.size()) - 1;
    assert(n >= 1);
    i64 res = 0;
    vector<int> tmp(n + 1);

    function<void(int, int)> mSort = [&](int l, int r) {
        if (l >= r) return;
        int m = (l + r) / 2;
        mSort(l, m), mSort(m + 1, r);

        int k = 0, i = l, j = m + 1;
        while (i <= m and j <= r) {
            if (vec[i] <= vec[j]) {
                tmp[k++] = vec[i++];
            } else {
                tmp[k++] = vec[j++];
                res += m - i + 1;
            }
        }
        while (i <= m) {
            tmp[k++] = vec[i++];
        }
        while (j <= r) {
            tmp[k++] = vec[j++];
        }

        for (i = l, j = 0; i <= r; i++, j++) {
            vec[i] = tmp[j];
        }
    };
    mSort(1, n);

    return res;
};
```



## topo sort

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

