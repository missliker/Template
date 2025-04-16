# 单点修改

[P3374树状数组1](https://www.luogu.com.cn/problem/P3374)

看似是树状数组模板题，但是完美符合我们最基础的线段树单点修改，区间查询的要求

## 单点加,查询区间加

### 模板

```cpp
constexpr i64 P = 998244353;
struct SegmentTree {
    int n;
    vector<int> tag, sum;
    SegmentTree(int n_) : n(n_), tag(4 * n, 1), sum(4 * n) {}

    void pull(int p) { sum[p] = (sum[2 * p] + sum[2 * p + 1]) % P; }

    void mul(int p, int v) {
        tag[p] = 1LL * tag[p] * v % P;
        sum[p] = 1LL * sum[p] * v % P;
    }

    void push(int p) {
        mul(2 * p, tag[p]);
        mul(2 * p + 1, tag[p]);
        tag[p] = 1;
    }

    int rangeSum(int p, int l, int r, int x, int y) {
        if (l > y or r < x) return 0;
        if (l >= x and r <= y) return sum[p];
        int m = (l + r) / 2;
        push(p);
        return (rangeSum(2 * p, l, m, x, y) + rangeSum(2 * p + 1, m + 1, r, x, y)) % P;
    }

    int rangeSum(int x, int y) { return rangeSum(1, 1, n, x, y); }

    void rangeMul(int p, int l, int r, int x, int y, int v) {
        if (l > y or r < x) return;
        if (l >= x and r <= y) return mul(p, v);
        int m = (l + r) / 2;
        push(p);
        rangeMul(2 * p, l, m, x, y, v);
        rangeMul(2 * p + 1, m + 1, r, x, y, v);
        pull(p);
    }

    void rangeMul(int x, int y, int v) { rangeMul(1, 1, n, x, y, v); }

    void add(int p, int l, int r, int x, int v) {
        if (l == r) {
            sum[p] = (sum[p] + v) % P;
            return;
        }
        int m = (l + r) / 2;
        push(p);
        if (x <= m) {
            add(2 * p, l, m, x, v);
        } else {
            add(2 * p + 1, m + 1, r, x, v);
        }
        pull(p);
    }

    void add(int x, int v) { add(1, 1, n, x, v); }
};
```

### 实际使用

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q;
    SegmentTree seg(n + 1);
    for (int i = 1; i <= n; i++) {
        int x;
        cin >> x;
        seg.add(i, x);
    }
    while (q--) {
        int o, x, k, l, r;
        cin >> o;
        if (o == 1) {
            cin >> x >> k;
            seg.add(x, k);
        } else {
            cin >> l >> r;
            cout << seg.rangeSum(l, r) << '\n';
        }
    }

    return 0;
}
```

## 单点修改,查询区间最大子段和

[SP1716](https://www.luogu.com.cn/problem/SP1716)

### 模板

```cpp
template <class Info>
struct SegmentTree {
    int n;
    vector<Info> info;
    SegmentTree() : n(0) {}
    SegmentTree(int n_, Info v_ = Info()) { init(n_, v_); }
    template <class T>
    SegmentTree(vector<T> init_) {
        init(init_);
    }
    void init(int n_, Info v_ = Info()) { init(vector(n_, v_)); }

    template <class T>
    void init(vector<T> init_) {
        n = init_.size();
        info.assign(4 << __lg(n), Info());
        function<void(int, int, int)> build = [&](int p, int l, int r) {
            if (l == r) {
                info[p] = init_[l];
                return;
            }
            int m = (l + r) / 2;
            build(2 * p, l, m);
            build(2 * p + 1, m + 1, r);
            pull(p);
        };
        build(1, 1, n);
    }

    void pull(int p) { info[p] = info[2 * p] + info[2 * p + 1]; }

    void modify(int p, int l, int r, int x, const Info& v) {
        if (l == r) {
            info[p] = v;
            return;
        }
        int m = (l + r) / 2;
        if (x <= m) {
            modify(2 * p, l, m, x, v);
        } else {
            modify(2 * p + 1, m + 1, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info& v) { modify(1, 1, n, p, v); }

    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l > y or r < x) return Info();
        if (l >= x and r <= y) return info[p];
        int m = (l + r) / 2;
        return rangeQuery(2 * p, l, m, x, y) + rangeQuery(2 * p + 1, m + 1, r, x, y);
    }
    Info rangeQuery(int l, int r) { return rangeQuery(1, 1, n, l, r); }

    template <class F>
    int findFirst(int p, int l, int r, int x, int y, F pred) {
        if (l > y or r < x or !pred(info[p])) { return -1; }
        if (l == r) { return l; }
        int m = (l + r) / 2;
        int res = findFirst(2 * p, l, m, x, y, pred);
        if (res == -1) { res = findFirst(2 * p + 1, m + 1, r, x, y, pred); }
        return res;
    }
    template <class F>
    int findFirst(int l, int r, F pred) {
        return findFirst(1, 1, n, l, r, pred);
    }

    template <class F>
    int findLast(int p, int l, int r, int x, int y, F pred) {
        if (l > y or r < x or !pred(info[p])) { return -1; }
        if (l == r) { return l; }
        int m = (l + r) / 2;
        int res = findLast(2 * p + 1, m + 1, r, x, y, pred);
        if (res == -1) { res = findLast(2 * p, l, m + 1, x, y, pred); }
        return res;
    }
    template <class F>
    int findLast(int l, int r, F pred) {
        return findLast(1, 1, n, l, r, pred);
    }
};

constexpr i64 INF = 1E18 + 10;

struct Info {
    i64 mx = -INF;
    i64 sum = -INF;
    i64 lmx = -INF;
    i64 rmx = -INF;
};

Info operator+(Info a, Info b) {
    Info res;
    res.sum = a.sum + b.sum;
    res.mx = max({a.mx, b.mx, a.rmx + b.lmx});
    res.lmx = max(a.lmx, a.sum + b.lmx);
    res.rmx = max(b.rmx, b.sum + a.rmx);
    return res;
}
```

### 实际使用

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n;
    vector<Info> init(n + 1);
    for (int i = 1; i <= n; i++) {
        int A;
        cin >> A;
        init[i] = {A, A, A, A};
    }
    SegmentTree<Info> seg(init);

    cin >> q;
    while (q--) {
        int o, l, r, x, y;
        cin >> o;
        if (o == 0) {
            cin >> x >> y;
            seg.modify(x, {y, y, y, y});
        } else {
            cin >> l >> r;
            if (l > r) swap(l, r);
            cout << seg.rangeQuery(l, r).mx << '\n';
        }
    }

    return 0;
}
```



## 单点修改,查询区间次大值的个数

[ABC343F](https://atcoder.jp/contests/abc343/tasks/abc343_f)

### 模板

```cpp
template <class Info>
struct SegmentTree {
    int n;
    vector<Info> info;
    SegmentTree() : n(0) {}
    SegmentTree(int n_, Info v_ = Info()) { init(n_, v_); }
    template <class T>
    SegmentTree(vector<T> init_) {
        init(init_);
    }
    void init(int n_, Info v_ = Info()) { init(vector(n_, v_)); }

    template <class T>
    void init(vector<T> init_) {
        n = init_.size();
        info.assign(4 << __lg(n), Info());
        function<void(int, int, int)> build = [&](int p, int l, int r) {
            if (l == r) {
                info[p] = init_[l];
                return;
            }
            int m = (l + r) / 2;
            build(2 * p, l, m);
            build(2 * p + 1, m + 1, r);
            pull(p);
        };
        build(1, 1, n);
    }

    void pull(int p) { info[p] = info[2 * p] + info[2 * p + 1]; }

    void modify(int p, int l, int r, int x, const Info& v) {
        if (l == r) {
            info[p] = v;
            return;
        }
        int m = (l + r) / 2;
        if (x <= m) {
            modify(2 * p, l, m, x, v);
        } else {
            modify(2 * p + 1, m + 1, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info& v) { modify(1, 1, n, p, v); }

    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l > y or r < x) return Info();
        if (l >= x and r <= y) return info[p];
        int m = (l + r) / 2;
        return rangeQuery(2 * p, l, m, x, y) + rangeQuery(2 * p + 1, m + 1, r, x, y);
    }
    Info rangeQuery(int l, int r) { return rangeQuery(1, 1, n, l, r); }

    template <class F>
    int findFirst(int p, int l, int r, int x, int y, F pred) {
        if (l > y or r < x or !pred(info[p])) { return -1; }
        if (l == r) { return l; }
        int m = (l + r) / 2;
        int res = findFirst(2 * p, l, m, x, y, pred);
        if (res == -1) { res = findFirst(2 * p + 1, m + 1, r, x, y, pred); }
        return res;
    }
    template <class F>
    int findFirst(int l, int r, F pred) {
        return findFirst(1, 1, n, l, r, pred);
    }
    
    template <class F>
    int findLast(int p, int l, int r, int x, int y, F pred) {
        if (l > y or r < x or !pred(info[p])) { return -1; }
        if (l == r) { return l; }
        int m = (l + r) / 2;
        int res = findLast(2 * p + 1, m + 1, r, x, y, pred);
        if (res == -1) { res = findLast(2 * p, l, m + 1, x, y, pred); }
        return res;
    }
    template <class F>
    int findLast(int l, int r, F pred) {
        return findLast(1, 1, n, l, r, pred);
    }
};

struct Info {
    int mx1 = -1;
    int cnt1 = 0;
    int mx2 = -2;
    int cnt2 = 0;
};

Info operator+(Info a, Info b) {
    if (a.mx1 == b.mx1) {
        if (a.mx2 < b.mx2) { swap(a, b); }
        a.cnt1 += b.cnt1;
        if (a.mx2 == b.mx2) { a.cnt2 += b.cnt2; }
        return a;
    }
    if (a.mx1 < b.mx1) { swap(a, b); }
    if (b.mx1 > a.mx2) {
        a.mx2 = b.mx1;
        a.cnt2 = b.cnt1;
    } else if (b.mx1 == a.mx2) {
        a.cnt2 += b.cnt1;
    }
    return a;
}
```

### 实际使用

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q;
    SegmentTree<Info> seg(n + 1);
    for (int i = 1; i <= n; i++) {
        int x;
        cin >> x;
        seg.modify(i, {x, 1, -1, 0});
    }
    while (q--) {
        int o, l, r, p, x;
        cin >> o;
        if (o == 1) {
            cin >> p >> x;
            seg.modify(p, {x, 1, -1, 0});
        } else {
            cin >> l >> r;
            cout << seg.rangeQuery(l, r).cnt2 << '\n';
        }
    }

    return 0;
}
```

## 单点修改, 查询子串是否回文 (字符串)

### 模板

```cpp
template <class Info>
struct SegmentTree {
    int n;
    vector<Info> info;
    SegmentTree() : n(0) {}
    SegmentTree(int n_, Info v_ = Info()) { init(n_, v_); }
    template <class T>
    SegmentTree(vector<T> init_) {
        init(init_);
    }
    void init(int n_, Info v_ = Info()) { init(vector(n_, v_)); }

    template <class T>
    void init(vector<T> init_) {
        n = init_.size();
        info.assign(4 << __lg(n), Info());
        function<void(int, int, int)> build = [&](int p, int l, int r) {
            if (l == r) {
                info[p] = init_[l];
                return;
            }
            int m = (l + r) / 2;
            build(2 * p, l, m);
            build(2 * p + 1, m + 1, r);
            pull(p);
        };
        build(1, 1, n);
    }

    void pull(int p) { info[p] = info[2 * p] + info[2 * p + 1]; }

    void modify(int p, int l, int r, int x, const Info& v) {
        if (l == r) {
            info[p] = v;
            return;
        }
        int m = (l + r) / 2;
        if (x <= m) {
            modify(2 * p, l, m, x, v);
        } else {
            modify(2 * p + 1, m + 1, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info& v) { modify(1, 1, n, p, v); }

    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l > y or r < x) return Info();
        if (l >= x and r <= y) return info[p];
        int m = (l + r) / 2;
        return rangeQuery(2 * p, l, m, x, y) + rangeQuery(2 * p + 1, m + 1, r, x, y);
    }
    Info rangeQuery(int l, int r) { return rangeQuery(1, 1, n, l, r); }

    template <class F>
    int findFirst(int p, int l, int r, int x, int y, F pred) {
        if (l > y or r < x or !pred(info[p])) { return -1; }
        if (l == r) { return l; }
        int m = (l + r) / 2;
        int res = findFirst(2 * p, l, m, x, y, pred);
        if (res == -1) { res = findFirst(2 * p + 1, m + 1, r, x, y, pred); }
        return res;
    }
    template <class F>
    int findFirst(int l, int r, F pred) {
        return findFirst(1, 1, n, l, r, pred);
    }

    template <class F>
    int findLast(int p, int l, int r, int x, int y, F pred) {
        if (l > y or r < x or !pred(info[p])) { return -1; }
        if (l == r) { return l; }
        int m = (l + r) / 2;
        int res = findLast(2 * p + 1, m + 1, r, x, y, pred);
        if (res == -1) { res = findLast(2 * p, l, m + 1, x, y, pred); }
        return res;
    }
    template <class F>
    int findLast(int l, int r, F pred) {
        return findLast(1, 1, n, l, r, pred);
    }
};

template <class T>
constexpr T power(T a, i64 b) {
    T res = 1;
    for (; b; b /= 2, a *= a) {
        if (b % 2) { res *= a; }
    }
    return res;
}

constexpr i64 mul(i64 a, i64 b, i64 p) {
    i64 res = a * b - i64(1.L * a * b / p) * p;
    res %= p;
    if (res < 0) { res += p; }
    return res;
}
template <i64 P>
struct MLong {
    i64 x;
    constexpr MLong() : x{} {}
    constexpr MLong(i64 x) : x{norm(x % getMod())} {}

    static i64 Mod;
    constexpr static i64 getMod() {
        if (P > 0) {
            return P;
        } else {
            return Mod;
        }
    }
    constexpr static void setMod(i64 Mod_) { Mod = Mod_; }
    constexpr i64 norm(i64 x) const {
        if (x < 0) { x += getMod(); }
        if (x >= getMod()) { x -= getMod(); }
        return x;
    }
    constexpr i64 val() const { return x; }
    explicit constexpr operator i64() const { return x; }
    constexpr MLong operator-() const {
        MLong res;
        res.x = norm(getMod() - x);
        return res;
    }
    constexpr MLong inv() const {
        assert(x != 0);
        return power(*this, getMod() - 2);
    }
    constexpr MLong& operator*=(MLong rhs) & {
        x = mul(x, rhs.x, getMod());
        return *this;
    }
    constexpr MLong& operator+=(MLong rhs) & {
        x = norm(x + rhs.x);
        return *this;
    }
    constexpr MLong& operator-=(MLong rhs) & {
        x = norm(x - rhs.x);
        return *this;
    }
    constexpr MLong& operator/=(MLong rhs) & { return *this *= rhs.inv(); }
    friend constexpr MLong operator*(MLong lhs, MLong rhs) {
        MLong res = lhs;
        res *= rhs;
        return res;
    }
    friend constexpr MLong operator+(MLong lhs, MLong rhs) {
        MLong res = lhs;
        res += rhs;
        return res;
    }
    friend constexpr MLong operator-(MLong lhs, MLong rhs) {
        MLong res = lhs;
        res -= rhs;
        return res;
    }
    friend constexpr MLong operator/(MLong lhs, MLong rhs) {
        MLong res = lhs;
        res /= rhs;
        return res;
    }
    friend constexpr std::istream& operator>>(std::istream& is, MLong& a) {
        i64 v;
        is >> v;
        a = MLong(v);
        return is;
    }
    friend constexpr std::ostream& operator<<(std::ostream& os, const MLong& a) { return os << a.val(); }
    friend constexpr bool operator==(MLong lhs, MLong rhs) { return lhs.val() == rhs.val(); }
    friend constexpr bool operator!=(MLong lhs, MLong rhs) { return lhs.val() != rhs.val(); }
};

template <>
i64 MLong<0LL>::Mod = i64(1e18) + 9;

template <int P>
struct MInt {
    int x;
    constexpr MInt() : x{} {}
    constexpr MInt(i64 x) : x{norm(x % getMod())} {}

    static int Mod;
    constexpr static int getMod() {
        if (P > 0) {
            return P;
        } else {
            return Mod;
        }
    }
    constexpr static void setMod(int Mod_) { Mod = Mod_; }
    constexpr int norm(int x) const {
        if (x < 0) { x += getMod(); }
        if (x >= getMod()) { x -= getMod(); }
        return x;
    }
    constexpr int val() const { return x; }
    explicit constexpr operator int() const { return x; }
    constexpr MInt operator-() const {
        MInt res;
        res.x = norm(getMod() - x);
        return res;
    }
    constexpr MInt inv() const {
        assert(x != 0);
        return power(*this, getMod() - 2);
    }
    constexpr MInt& operator*=(MInt rhs) & {
        x = 1LL * x * rhs.x % getMod();
        return *this;
    }
    constexpr MInt& operator+=(MInt rhs) & {
        x = norm(x + rhs.x);
        return *this;
    }
    constexpr MInt& operator-=(MInt rhs) & {
        x = norm(x - rhs.x);
        return *this;
    }
    constexpr MInt& operator/=(MInt rhs) & { return *this *= rhs.inv(); }
    friend constexpr MInt operator*(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res *= rhs;
        return res;
    }
    friend constexpr MInt operator+(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res += rhs;
        return res;
    }
    friend constexpr MInt operator-(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res -= rhs;
        return res;
    }
    friend constexpr MInt operator/(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res /= rhs;
        return res;
    }
    friend constexpr std::istream& operator>>(std::istream& is, MInt& a) {
        i64 v;
        is >> v;
        a = MInt(v);
        return is;
    }
    friend constexpr std::ostream& operator<<(std::ostream& os, const MInt& a) { return os << a.val(); }
    friend constexpr bool operator==(MInt lhs, MInt rhs) { return lhs.val() == rhs.val(); }
    friend constexpr bool operator!=(MInt lhs, MInt rhs) { return lhs.val() != rhs.val(); }
};

template <>
int MInt<0>::Mod = 998244353;

template <int V, int P>
constexpr MInt<P> CInv = MInt<P>(V).inv();

constexpr i64 P = i64(1E18) + 39;
constexpr int B = 114514;
using Z = MLong<P>;

struct Info {
    Z pw = 1;
    Z l = 0;
    Z r = 0;
};

Info operator+(Info a, Info b) { return {a.pw * b.pw, a.l * b.pw + b.l, b.r * a.pw + a.r}; }
```

### 实际使用

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int N, Q;
    string S;
    cin >> N >> Q >> S;
    SegmentTree<Info> seg(N + 1);
    for (int i = 1; i <= N; i++) seg.modify(i, {B, S[i - 1], S[i - 1]});

    while (Q--) {
        int o, l, r, x;
        char c;
        cin >> o;
        if (o == 1) {
            cin >> x >> c;
            seg.modify(x, {B, c, c});
        } else {
            cin >> l >> r;
            auto ans = seg.rangeQuery(l, r);
            cout << (ans.l == ans.r ? "Yes" : "No") << '\n';
        }
    }

    return 0;
}
```



# 区间修改

## 区间加,查询区间和

### 模板

```cpp
template <class Info, class Tag>
struct LazySegmentTree {
    const int n;
    vector<Info> info;
    vector<Tag> tag;
    LazySegmentTree(int n) : n(n), info(4 << __lg(n)), tag(4 << __lg(n)) {}
    LazySegmentTree(vector<Info> init) : LazySegmentTree(init.size()) {
        function<void(int, int, int)> build = [&](int p, int l, int r) {
            if (l == r) {
                info[p] = init[l];
                return;
            }
            int m = (l + r) / 2;
            build(2 * p, l, m);
            build(2 * p + 1, m + 1, r);
            pull(p);
        };
        build(1, 1, n);
    }

    void pull(int p) { info[p] = info[2 * p] + info[2 * p + 1]; }

    void apply(int p, const Tag& v) {
        info[p].apply(v);
        tag[p].apply(v);
    }

    void push(int p) {
        apply(2 * p, tag[p]);
        apply(2 * p + 1, tag[p]);
        tag[p] = Tag();
    }

    void modify(int p, int l, int r, int x, const Info& v) {
        if (l == r) {
            info[p] = v;
            return;
        }
        int m = (l + r) / 2;
        push(p);
        if (x <= m) modify(2 * p, l, m, x, v);
        else modify(2 * p + 1, m + 1, r, x, v);
        pull(p);
    }
    void modify(int p, const Info& v) { modify(1, 1, n, p, v); }

    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l > y or r < x) return Info();
        if (l >= x and r <= y) return info[p];
        int m = (l + r) / 2;
        push(p);
        return rangeQuery(2 * p, l, m, x, y) + rangeQuery(2 * p + 1, m + 1, r, x, y);
    }
    Info rangeQuery(int l, int r) { return rangeQuery(1, 1, n, l, r); }

    void rangeApply(int p, int l, int r, int x, int y, const Tag& v) {
        if (l > y or r < x) return;
        if (l >= x and r <= y) {
            apply(p, v);
            return;
        }
        int m = (l + r) / 2;
        push(p);
        rangeApply(2 * p, l, m, x, y, v);
        rangeApply(2 * p + 1, m + 1, r, x, y, v);
        pull(p);
    }
    void rangeApply(int l, int r, const Tag& v) { return rangeApply(1, 1, n, l, r, v); }

    void half(int p, int l, int r) {
        if (info[p].act == 0) return;
        if ((info[p].min + 1) / 2 == (info[p].max + 1) / 2) {
            apply(p, {-(info[p].min + 1) / 2});
            return;
        }
        int m = (l + r) / 2;
        push(p);
        half(2 * p, l, m);
        half(2 * p + 1, m + 1, r);
        pull(p);
    }
    void half() { half(1, 1, n); }
};

constexpr i64 INF = 1E18;

struct Tag {
    i64 add = 0;
    void apply(Tag t) { add += t.add; }
};

struct Info {
    i64 min = INF;
    i64 max = -INF;
    i64 sum = 0;
    i64 act = 1;

    void apply(Tag t) {
        min += t.add;
        max += t.add;
        sum += act * t.add;
    }
};

Info operator+(Info a, Info b) {
    Info c;
    c.min = min(a.min, b.min);
    c.max = max(a.max, b.max);
    c.sum = a.sum + b.sum;
    c.act = a.act + b.act;
    return c;
}
```

### 实际用法

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q;
    LazySegmentTree<Info, Tag> seg(n + 1);
    for (int i = 1; i <= n; i++) {
        int x;
        cin >> x;
        seg.rangeApply(i, i, {x});
    }

    while (q--) {
        int o, l, r, k;
        cin >> o;
        if (o == 1) {
            cin >> l >> r >> k;
            seg.rangeApply(l, r, {k});
        } else {
            cin >> l >> r;
            cout << seg.rangeQuery(l, r).sum << '\n';
        }
    }

    return 0;
}
```

## 区间加,区间乘,查询区间和

### 模板

```cpp
template <class Info, class Tag>
struct LazySegmentTree {
    const int n;
    vector<Info> info;
    vector<Tag> tag;
    LazySegmentTree(int n) : n(n), info(4 << __lg(n)), tag(4 << __lg(n)) {}
    LazySegmentTree(vector<Info> init) : LazySegmentTree(init.size()) {
        function<void(int, int, int)> build = [&](int p, int l, int r) {
            if (l == r) {
                info[p] = init[l];
                return;
            }
            int m = (l + r) / 2;
            build(2 * p, l, m);
            build(2 * p + 1, m + 1, r);
            pull(p);
        };
        build(1, 1, n);
    }

    void pull(int p) { info[p] = info[2 * p] + info[2 * p + 1]; }

    void apply(int p, const Tag& v) {
        info[p].apply(v);
        tag[p].apply(v);
    }

    void push(int p) {
        apply(2 * p, tag[p]);
        apply(2 * p + 1, tag[p]);
        tag[p] = Tag();
    }

    void modify(int p, int l, int r, int x, const Info& v) {
        if (l == r) {
            info[p] = v;
            return;
        }
        int m = (l + r) / 2;
        push(p);
        if (x <= m) modify(2 * p, l, m, x, v);
        else modify(2 * p + 1, m + 1, r, x, v);
        pull(p);
    }
    void modify(int p, const Info& v) { modify(1, 1, n, p, v); }

    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l > y or r < x) return Info();
        if (l >= x and r <= y) return info[p];
        int m = (l + r) / 2;
        push(p);
        return rangeQuery(2 * p, l, m, x, y) + rangeQuery(2 * p + 1, m + 1, r, x, y);
    }
    Info rangeQuery(int l, int r) { return rangeQuery(1, 1, n, l, r); }

    void rangeApply(int p, int l, int r, int x, int y, const Tag& v) {
        if (l > y or r < x) return;
        if (l >= x and r <= y) {
            apply(p, v);
            return;
        }
        int m = (l + r) / 2;
        push(p);
        rangeApply(2 * p, l, m, x, y, v);
        rangeApply(2 * p + 1, m + 1, r, x, y, v);
        pull(p);
    }
    void rangeApply(int l, int r, const Tag& v) { return rangeApply(1, 1, n, l, r, v); }

    void half(int p, int l, int r) {
        if (info[p].act == 0) return;
        if ((info[p].min + 1) / 2 == (info[p].max + 1) / 2) {
            apply(p, {-(info[p].min + 1) / 2});
            return;
        }
        int m = (l + r) / 2;
        push(p);
        half(2 * p, l, m);
        half(2 * p + 1, m + 1, r);
        pull(p);
    }
    void half() { half(1, 1, n); }
};

constexpr i64 INF = 1E18;
int mod;

struct Tag {
    i64 add = 0, mul = 1;
    // (res * a.mul + a.add) * b.mul + b.add = res * a.mul * b.mul + a.add * b.mul + b.add
    void apply(Tag t) {
        add = ((add * t.mul) % mod + t.add) % mod;
        mul = mul * t.mul % mod;
    }
};

struct Info {
    i64 min = INF;
    i64 max = -INF;
    i64 sum = 0;
    i64 act = 1;

    void apply(Tag t) {
        min = min * t.mul + t.add;
        max = max * t.mul + t.add;
        sum = ((sum * t.mul) % mod + act * t.add) % mod;
    }
};

Info operator+(Info a, Info b) {
    Info c;
    c.min = min(a.min, b.min);
    c.max = max(a.max, b.max);
    c.sum = (a.sum + b.sum) % mod;
    c.act = a.act + b.act;
    return c;
}
```

### 实际用法

```cpp
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n, q;
    cin >> n >> q >> mod;
    LazySegmentTree<Info, Tag> seg(n + 1);
    for (int i = 1; i <= n; i++) {
        int x;
        cin >> x;
        seg.rangeApply(i, i, {x});
    }
    while (q--) {
        int o, l, r, k;
        cin >> o;
        if (o == 3) {
            cin >> l >> r;
            cout << seg.rangeQuery(l, r).sum << '\n';
        } else {
            cin >> l >> r >> k;
            if (o == 1) {
                seg.rangeApply(l, r, {0, k});
            } else {
                seg.rangeApply(l, r, {k, 1});
            }
        }
    }

    return 0;
}
```

