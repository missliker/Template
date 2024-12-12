# 珂朵莉树/颜色段均摊

模板

```c++
struct ODT {
    struct Info {
        int l, r;
        mutable int v;
        Info(const int& l, const int& r, const int& v) : l(l), r(r), v(v) {}
        bool operator<(const Info& t) { return l < t.l; }
    };

    set<Info> odt;

    auto split(int x) {
        auto it = odt.lower_bound(Info{x, 0, 0});
        if (it != odt.end() and it->l == x) return it;
        it--;
        auto [l, r, v] = *it;
        odt.erase(it);
        odt.insert(Info{l, x - 1, v});
        return odt.insert(Info{x, r, v}).first;
    }

    void assign(int l, int r, int v) {
        auto it_r = split(r + 1), it_l = split(l);
        odt.erase(it_l, it_r);
        odt.insert(Info{l, r, v});
    }

    void perform(int l, int r) {
        auto it_r = split(r + 1), it_l = split(l);
        for (; it_l != it_r; it_l++) {
            // do something
        }
    }
};
```

[珂朵莉树起源](https://codeforces.com/problemset/problem/896/C)