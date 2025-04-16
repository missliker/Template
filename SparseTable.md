# 静态RMQ问题

复杂度：

预处理： $O(nlogn)$

查询： $O(1)$

## 模板

在不带修的区间查询最大最小值的时候可以达到O(1)的效果

```cpp
template <typename T>
struct SparseTable {
    int n;
    vector<int> lg;
    vector<vector<T>> info;
    using func_type = function<T(const T&, const T&)>;
    static T default_func(const T& x, const T& y) { return max(x, y); }

    func_type op;
    SparseTable() {}
    SparseTable(const vector<T>& v, func_type _func = default_func) { init(v, _func); }

    void init(const vector<T>& in, func_type _func) {
        op = _func;
        n = in.size();

        lg.assign(n + 1, 0);
        for (int i = 2; i <= n; i++) {
            lg[i] = lg[i / 2] + 1;
        }

        info.assign(lg[n] + 1, vector<T>(n));
        for (int i = 0; i < n; i++) {
            info[0][i] = in[i];
        }

        for (int i = 1; (1 << i) <= n; i++) {
            for (int j = 0; j + (1 << i) - 1 < n; j++) {
                info[i][j] = op(info[i - 1][j], info[i - 1][j + (1 << (i - 1))]);
            }
        }
    }

    T operator()(int l, int r) {
        assert(l <= r);
        int k = lg[r - l + 1];
        return op(info[k][l], info[k][r - (1 << k) + 1]);
    }
};
```

[Staic RMQ](https://judge.yosupo.jp/problem/staticrmq)

## 用法

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q;
    vector<i64> a(n + 1);
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    SparseTable<i64> st(a, [&](i64 x, i64 y) { return min(x, y); });
    // 这里SparseTable的类型要和a的类型一致，否则会报错

    while (q--) {
        int l, r;
        cin >> l >> r;
        cout << st(l, r) << '\n';
    }

    return 0;
}
```
