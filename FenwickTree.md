# 树状数组

## 模板

```cpp
template <typename T>
struct Fenwick {
    int n;
    vector<T> a;

    Fenwick(int n_ = 0) { init(n_); }

    void init(int n_) {
        n = n_;
        a.assign(n, T{});
    }

    void add(int x, const T& v) {
        assert(x != 0);
        for (; x <= n; x += x & -x) { a[x] = a[x] + v; }
    }

    T sum(int x) {
        assert(x <= n);
        T ans{};
        for (; x; x -= x & -x) { ans = ans + a[x]; }
        return ans;
    }

    T rangeSum(int l, int r) { return sum(r) - sum(l - 1); }

    int select(T s) {
        int p = 0;
        for (int i = (1 << __lg(n)) + 1; i; i >>= 1) {
            if (p + i <= n and a[p + i] <= s) {
                p += i;
                s = s - a[p];
            }
        }
        return p;
    }
};
```

## 具体使用

```cpp
// select()函数是找到一个最大的下标p,使得sum(p)<=s
```

## 点修区查

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(10);

    int n, q;
    cin >> n >> q;
    Fenwick<i64> bit(n + 1);
    for (int i = 1; i <= n; i++) {
        int x;
        bit.add(i, x);
    }
    while (q--) {
        int o, l, r, k;
        cin >> o;
        if (o == 1) {
            cin >> l >> k;
            bit.add(l, k);
        } else {
            cin >> l >> r;
            cout << bit.rangeSum(l, r) << '\n';
        }
    }

    return 0;
}
```

## 区修区查

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q;
    Fenwick<i64> d(n + 2), lazy(n + 2);

    auto add = [&](int l, int r, i64 k) {
        d.add(l, k), d.add(r + 1, -k);
        lazy.add(l, k * (l - 1)), lazy.add(r + 1, -k * r);
    };

    auto sum = [&](int x) {
        return d.sum(x) * x - lazy.sum(x);
    };

    auto rangeSum = [&](int l, int r) {
        return sum(r) - sum(l - 1);
    };

    for (int i = 1; i <= n; i++) {
        int x;
        cin >> x;
        add(i, i, x);
    }
    while (q--) {
        int o, l, r, k;
        cin >> o;
        if (o == 1) {
            cin >> l >> r >> k;
            add(l, r, k);
        } else {
            cin >> l >> r;
            cout << rangeSum(l, r) << '\n';
        }
    }

    return 0;
}
```



# 树状数组(求逆序对)

```cpp
i64 c[N];
i64 n,m;

struct node {
	i64 val,id;
};

node a[N];

i64 query(int x) {
	i64 s = 0;
	for(; x;x -= x & (-x)) s += c[x];
	return s;
}

void modify(int x,i64 s) {
	for(; x <= n; x += x & (-x)) c[x] += s;
}

void solve() {
	cin >> n;
	for(int i = 1;i <= n;i++) {
		cin >> a[i].val;
		a[i].id = i;
	}

	auto cmp = [&](node x,node y) {
		if(x.val == y.val) return x.id > y.id;
		return x.val > y.val;
	};

	sort(a + 1,a + n + 1,cmp);

	i64 ans = 0;
	for(int i = 1;i <= n;i++) {
		ans += query(a[i].id);
		modify(a[i].id,1);
	}
	cout << ans << "\n";
}
```

