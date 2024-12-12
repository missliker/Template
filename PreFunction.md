# 自定义模板类函数

```c++
struct range_param {
    std::size_t begin{};
    std::size_t end{};
    std::size_t step{1};
};
auto range(std::size_t begin, std::size_t end, std::size_t step = 1) { return views::iota(begin, end) | views::stride(step); }
auto range(std::size_t end) { return views::iota(0UZ, end) | views::stride(1); }
auto range(range_param param) { return range(param.begin, param.end, param.step); }

// 更新最大值
// 用法：if (chmax(a, b)) {...}
bool chmax(auto& x, auto&& y) { return (x < y) ? (x = y, true) : false; }

// 更新最小值
// 用法：if (chmin(a, b)) {...}
bool chmin(auto& x, auto&& y) { return (x > y) ? (x = y, true) : false; }

// 找出容器最大值
// 用法：auto mx = max(v);
template <typename T>
auto max(const T& container) {
    if constexpr (not std::ranges::range<T>) {
        return container;
    } else {
        using U = decltype(max(*std::begin(container)));
        U res = numeric_limits<U>::lowest();
        for (const auto& sub_container : container) res = std::max(res, max(sub_container));
        return res;
    }
}

// 找出容器最小值
// 用法：auto mn = min(v);
template <typename T>
auto min(const T& container) {
    if constexpr (not std::ranges::range<T>) {
        return container;
    } else {
        using U = decltype(min(*std::begin(container)));
        U res = std::numeric_limits<U>::max();
        for (const auto& sub_container : container) res = std::min(res, min(sub_container));
        return res;
    }
}

// 计算容器元素和
// 用法：auto s = sum(v);
template <typename T>
auto sum(const T& container) {
    if constexpr (not std::ranges::range<T>) {
        return container;
    } else {
        decltype(sum(*std::begin(container))) res = 0;
        for (const auto& sub_container : container) res += sum(sub_container);
        return res;
    }
}

// 反转容器
// 用法：auto r = reversed(v);
template <typename T>
T reversed(T a) {
    std::ranges::reverse(a);
    return a;
}

// 去重容器
// 用法：auto u = uniqued(v);
template <typename T>
T uniqued(T a) {
    std::ranges::sort(a);
    a.erase(std::unique(a.begin(), a.end()), a.end());
    return a;
}

// 排序容器
// 用法：auto s = sorted(v, greater<>());
template <typename... Args>
auto sorted(auto a, Args&&... args) {
    std::ranges::sort(a, forward<Args>(args)...);
    return a;
}

// 创建多维向量
// 用法：auto vec = make_vector(3, 4, 0); // 创建3x4的二维向量，初始值为0
template <typename T>
auto make_vector(T x) {
    return x;
}

template <typename T1, typename T2, typename... Args>
auto make_vector(T1 m, T2 n, Args... arg) {
    return vector(m, make_vector(n, arg...));
}

// 转置矩阵
// 用法：auto t = transposed(matrix);
template <typename T>
T transposed(T a) {
    if (a.empty()) return a;
    size_t m = a.size(), n = a[0].size();
    T b(n, vector<typename T::value_type::value_type>(m));
    for (size_t j : range(n)) {
        for (size_t i : range(m)) {
            b[j][i] = a[i][j];
        }
    }
    return b;
}

// 打印元组
// 用法：cout << tuple << '\n';
template <typename Tuple, typename F, size_t... N>
void TupleCall(Tuple& t, F&& f, std::index_sequence<N...>) {
    (f(get<N>(t)), ...);
}

template <typename... Args>
std::ostream& operator<<(std::ostream& out, const std::tuple<Args...>& t) {
    TupleCall(t, [&](auto&& a) { out << a << ' '; }, make_index_sequence<sizeof...(Args)>{});
    return out;
}

// 打印pair
// 用法：cout << pair << '\n';
template <typename T1, typename T2>
std::ostream& operator<<(std::ostream& out, const std::pair<T1, T2>& t) {
    return out << t.first << ' ' << t.second;
}

// 打印vector
// 用法：cout << vector << '\n';
template <typename T>
std::ostream& operator<<(std::ostream& out, const std::vector<T>& v) {
    for (const auto& elem : v) out << elem << ' ';
    return out;
}

// 打印多参数
// 用法：print(a, b, c);
template <typename T, typename... Args>
void print(const T& t, const Args&... args) {
    cout << t;
    if constexpr (sizeof...(args)) {
        cout << ' ';
        print(args...);
    } else {
        cout << '\n';
    }
}

// 从标准输入读取数据
// 用法：auto n = rd<int>();
//      auto vec = rd<int>(n);
template <typename T = i64>
auto rd() {
    T tmp;
    cin >> tmp;
    return tmp;
}

// 读取多个值
// 用法：auto [a, b] = rd<2, int>();
template <size_t N, typename T = i64>
auto rd() {
    array<T, N> res;
    for (auto& x : res) cin >> x;
    return res;
}

// 读取n个值
// 用法：auto vec = rd<int>(n);
template <typename T = i64>
auto rd(size_t n, size_t off = 0) {
    vector<T> v(off + n);
    for (size_t i : range(off, off + n)) cin >> v[i];
    return v;
}
```

用法

**注意一定要放在include <debug.h>后**

```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n = rd();
    auto v = rd(n);
    print(v);
    auto v1 = reversed(v);
    print(v1);
    auto v2 = sorted(v);
    print(v2);
    auto v3 = sorted(v, greater<int>());
    print(v3);
    auto v4 = make_vector(3, 3, 1LL);
    print(v4);
    auto v5 = v4;
    for (int i : range(3)) {
        for (int j : range(3)) {
            v5[i][j] = i * 3 + j;
        }
    }
    print(v5);
    auto v6 = transposed(v5);
    print(v6);
    auto v7 = v3;
    for (auto i : range(v3.size())) {
        v7.emplace_back(v3[i]);
    }
    print(v7);
    auto v8 = uniqued(v7);
    print(v8);
    auto v9 = make_vector(3, 4, 0LL);
    for (auto i : range(3)) {
        for (auto j : range(4)) {
            v9[i][j] = i * 3 + j;
        }
    }
    print(v9);
    auto v10 = transposed(v9);
    print(v10);

    auto [xa, ya, xb, yb] = rd<4, int>();
    print(xa, ya, xb, yb);

    auto mx = max(v), mn = min(v);
    print(mx, mn);

    auto a = mn, b = mx;
    print(a, b);
    chmax(a, mx), chmin(b, mn);
    print(a, b);

    auto s = sum(v);
    print(s);
    auto s1 = sum(v6);
    print(s1);
    auto vs = make_vector(3, 4, 5, 0LL);
    for (auto i : range(3)) {
        for (auto j : range(4)) {
            for (auto k : range(5)) {
                vs[i][j][k] = Rand<int>(1, 50);
            }
        }
    }
    print(vs);
    print(max(vs));
    print(min(vs));
    
	auto tmp = max(vs);
    debug(tmp);

    return 0;
}
```



旧版

```c++
template <typename T>
auto make_vector(T x) {
    return x;
}

template <typename T1, typename T2, typename... Args>
auto make_vector(T1 m, T2 n, Args... arg) {
    return vector(n, make_vector(n, arg...));
}

struct range {
    struct Iterator {
        using iterator_category = std::random_access_iterator_tag;
        using value_type = i64;
        using difference_type = ptrdiff_t;
        using pointer = i64;
        using reference = i64&;
        i64 val, d;
        Iterator() {};
        Iterator(i64 val, i64 d) : val(val), d(d) {};
        value_type operator*() const { return val; }
        Iterator& operator++() { return val += d, *this; }
        difference_type operator-(const Iterator& other) const { return (val - other.val) / d; }
        bool operator==(const Iterator& other) const { return val == other.val; }
    };
    Iterator Begin, End;
    range(i64 n) : Begin(0, 1), End(max(n, i64{0}), 1) {};
    range(i64 a, i64 b, i64 d = i64(1)) : Begin(a, d), End(b, d) {
        i64 cnt = b == a or (b - a > 0) != (d > 0) ? 0 : (b - a) / d + bool((b - a) % d);
        End.val = a + max(cnt, i64(0)) * d;
    };
    Iterator begin() const { return Begin; }
    Iterator end() const { return End; };
    operator vector<Iterator::value_type>() { return vector(begin(), end()); }
};

bool chmax(auto& x, auto&& y) {
    if (x < y) {
        x = y;
        return true;
    }
    return false;
}

bool chmin(auto& x, auto&& y) {
    if (x > y) {
        x = y;
        return true;
    }
    return false;
}

template <typename T>
T sum(auto&& a, T res) {
    for (auto x : a) res += x;
    return res;
}

template <typename T>
i64 sum(T&& a) {
    return sum(forward<T>(a), 0ll);
}

template <typename T>
T reversed(T a) {
    ranges::reverse(a);
    return a;
}

template <typename T>
T uniqued(T a) {
    ranges::sort(a);
    a.resize(unique(begin(a), end(a)) - begin(a));
    return a;
}

template <typename... Args>
auto sorted(auto a, Args&&... args) {
    sort(begin(a), end(a), args...);
    return a;
}

template <typename T>
T transposed(T a) {
    if (a.empty()) return a;
    int m = a.size(), n = a[0].size();
    T b(n);
    for (int j : range(n)) {
        b[j].resize(m);
        for (int i : range(m)) b[j][i] = a[i][j];
    }
    return b;
}

template <typename Tuple, typename F, size_t... N>
void TupleCall(Tuple& t, F&& f, std::index_sequence<N...>) {
    (f(get<N>(t)), ...);
}

template <typename... Args>
std::ostream& operator<<(std::ostream& out, const std::tuple<Args...>& t) {
    TupleCall(t, [&](auto&& a) { out << a << ' '; }, make_index_sequence<sizeof...(Args)>{});
    return out;
}

template <typename T1, typename T2>
std::ostream& operator<<(std::ostream& out, const std::pair<T1, T2>& t) {
    return out << t.first << ' ' << t.second;
}

template <typename T>
std::ostream& operator<<(std::ostream& out, const std::vector<T>& v) {
    for (size_t i = 0, n = v.size(); i < n; i++) out << v[i] << ' ';
    return out;
}

template <typename T, typename... Args>
void print(const T& t, const Args&... args) {
    cout << t;
    if constexpr (sizeof...(args)) {
        cout << ' ';
        print(args...);
    } else {
        cout << '\n';
    }
}

template <typename T, std::size_t... Index>
auto TupleMake(std::index_sequence<Index...>) {
    return make_tuple((Index, T{})...);
}

template <typename... Args>
std::istream& operator>>(std::istream& in, std::tuple<Args...>& t) {
    TupleCall(t, [&](auto&& a) { in >> a; }, make_index_sequence<sizeof...(Args)>{});
    return in;
}

template <typename T = i64>
auto rd() {
    T tmp;
    std::cin >> tmp;
    return tmp;
}

template <size_t N, typename T = i64>
auto rd() {
    auto res = TupleMake<T>(std::make_index_sequence<N>{});
    std::cin >> res;
    return res;
};

template <typename T = i64>
auto rd(size_t n, size_t off = 0) {
    std::vector<T> v(off + n);
    for (size_t i = off; i < off + n; i++) cin >> v[i];
    return v;
}

template <typename T1, typename T2, typename... Args>
auto rd() {
    return rd<tuple<T1, T2, Args...>>();
}

template <typename T1, typename T2, typename... Args>
auto rd(size_t n, size_t off = 0) {
    return rd<tuple<T1, T2, Args...>>(n, off);
}
```
