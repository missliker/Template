# 珂朵莉树/颜色段均摊

[珂朵莉树起源](https://codeforces.com/problemset/problem/896/C)

模板化模板

```c++
template <typename T>
struct ODT {
    struct Info {
        int l, r;
        mutable T v;
        Info(const int& l, const int& r, const T& v) : l(l), r(r), v(v) {}
        bool operator<(const Info& t) const { return l < t.l; }
    };
    set<Info> odt;

    void insert(int l, int r, T v) { odt.insert(Info{l, r, v}); }

    auto split(int x) {
        auto it = odt.lower_bound(Info{x, 0, T{}});
        if (it != odt.end() and it->l == x) return it;
        it--;
        auto [l, r, v] = *it;
        if (r < x) return odt.end();
        odt.erase(it);
        odt.insert(Info{l, x - 1, v});
        return odt.insert(Info{x, r, v}).first;
    }

    void assign(int l, int r, const T& v) {
        auto itr = split(r + 1), itl = split(l);
        odt.erase(itl, itr);
        odt.insert(Info{l, r, v});
    }

    void rangeAdd(int l, int r, const T& v) {
        auto itr = split(r + 1), itl = split(l);
        for (auto it = itl; it != itr; it++) it->v += v;
    }

    i64 rangeKth(int l, int r, int k) {
        vector<pair<T, int>> v;
        auto itr = split(r + 1), itl = split(l);
        for (auto it = itl; it != itr; it++) {
            v.push_back({it->v, it->r - it->l + 1});
        }
        ranges::sort(v);
        for (auto [val, pos] : v) {
            if (pos < k) k -= pos;
            else return val;
        }
        return -1;
    } // 这题为什么rank会爆int...

    auto rangeSum(int l, int r, const i64 mod) {
        auto itr = split(r + 1), itl = split(l);
        T res{};
        for (auto it = itl; it != itr; it++) {
            res = (res + 1LL * (it->v % mod) * ((it->r - it->l + 1) % mod) % mod) % mod;
        }
        return res;
    }

    auto rangePowSum(int l, int r, const i64 p, const i64 mod) {
        auto power = [&](auto a, i64 b) {
            i64 res = 1;
            for (; b; a = 1LL * a * a % mod, b /= 2) {
                if (b % 2) res = (res * a) % mod;
            }
            return res;
        };
        // 注意这里写成lambda，是因为每次查询的mod都不同，不好只用两个参数的power
        // 没有直接用const i64 mod，也是因为每次查询的mod都不同

        auto itr = split(r + 1), itl = split(l);
        T res{};
        for (auto it = itl; it != itr; it++) {
            res = (res + 1LL * power(T{it->v % mod}, p) * ((it->r - it->l + 1LL) % mod) % mod) % mod;
        }
        return res;
    }
};
```

用法

```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    auto [n, m, seed, vmax] = rd<4>();

    auto rnd = [&]() {
        auto ret = seed;
        seed = (seed * 7 + 13) % 1000000007;
        return ret;
    };

    ODT<i64> odt;
    auto a = make_vector(n + 1, 0LL);
    for (int i : range(1, n + 1)) {
        a[i] = rnd() % vmax + 1;
        odt.insert(i, i, a[i]);
    }
    odt.insert(n + 1, n + 1, 0);

    while (m--) {
        auto o = rnd() % 4 + 1, l = rnd() % n + 1, r = rnd() % n + 1;
        if (l > r) swap(l, r);
        auto x = (o == 3 ? rnd() % (r - l + 1) + 1 : rnd() % vmax + 1);

        // if (o == 1) debug(o, l, r, x);
        // else if (o == 2) debug(o, l, r, x);
        // else if (o == 3) debug(o, l, r, x);
        // else if (o == 4) debug(o, l, r, x, y);

        if (o == 1) {
            odt.rangeAdd(l, r, x);
        } else if (o == 2) {
            odt.assign(l, r, x);
        } else if (o == 3) {
            print(odt.rangeKth(l, r, x));
        } else if (o == 4) {
            auto y = rnd() % vmax + 1;
            print(odt.rangePowSum(l, r, x, y));
        }
    }

    return 0;
}
```





旧的模板

```c++
struct ODT {
    struct Info {
        int l, r;
        mutable i64 v;
        Info(const int& l, const int& r, const i64& v) : l(l), r(r), v(v) {}
        bool operator<(const Info& t) const { return l < t.l; }
    };
    set<Info> odt;

    void insert(int l, int r, i64 v) { odt.insert(Info{l, r, v}); }

    auto split(int x) {
        auto it = odt.lower_bound(Info{x, 0, 0});
        if (it != odt.end() and it->l == x) return it;
        it--;
        auto [l, r, v] = *it;
        if (r < x) return odt.end();
        odt.erase(it);
        odt.insert(Info{l, x - 1, v});
        return odt.insert(Info{x, r, v}).first;
    }

    void assign(int l, int r, i64 v) {
        auto itr = split(r + 1), itl = split(l);
        odt.erase(itl, itr);
        odt.insert(Info{l, r, v});
    }

    void perform(int l, int r) {
        auto itr = split(r + 1), itl = split(l);
        for (auto it = itl; it != itr; it++) {
            // do something
        }
    }

    void rangeAdd(int l, int r, i64 v) {
        auto itr = split(r + 1), itl = split(l);
        for (auto it = itl; it != itr; it++) it->v += v;
    }

    i64 rangeKth(int l, int r, int k) {
        vector<array<i64, 2>> v;
        auto itr = split(r + 1), itl = split(l);
        for (auto it = itl; it != itr; it++) {
            v.push_back({it->v, it->r - it->l + 1});
        }
        ranges::sort(v);
        for (auto [val, pos] : v) {
            if (pos < k) k -= pos;
            else return val;
        }
        return -1;
    }

    auto rangeSum(int l, int r, const int mod) {
        auto itr = split(r + 1), itl = split(l);
        i64 res = 0;
        for (auto it = itl; it != itr; it++) {
            res = (res + (it->v % mod) * (it->r - it->l + 1) % mod) % mod;
        }
        return res;
    }

    auto rangePowSum(int l, int r, i64 p, const int mod) {
        auto power = [&](auto a, i64 b) {
            i64 res = 1;
            for (; b; a = 1LL * a * a % mod, b /= 2) {
                if (b % 2) res = (res * a) % mod;
            }
            return res;
        };
        // 注意这里写成lambda，是因为每次查询的mod都不同，不好只用两个参数的power
        // 没有直接用const i64 mod，也是因为每次查询的mod都不同

        auto itr = split(r + 1), itl = split(l);
        i64 res = 0;
        for (auto it = itl; it != itr; it++) {
            res = (res + power(it->v % mod, p) * (it->r - it->l + 1) % mod) % mod;
        }
        return res;
    }
};
```
