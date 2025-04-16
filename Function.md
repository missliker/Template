# 常用函数和自定义模板类函数

> [!Tip]
> 所有带ed的函数，均非在本身数据结构上修改，需要修改本身需要赋值
> 所有函数最好都放在#include "debug.h"后
## range函数
```cpp
struct range_param {
    std::size_t begin{};
    std::size_t end{};
    std::size_t step{1};
};
auto range(std::size_t begin, std::size_t end, std::size_t step = 1) { return views::iota(begin, end) | views::stride(step); }
auto range(std::size_t end) { return views::iota(0UZ, end) | views::stride(1); }
auto range(range_param param) { return range(param.begin, param.end, param.step); }
```

部分低版本可以使用不支持step为负数的版本：
```cpp
struct range_param {
    std::size_t begin{};
    std::size_t end{};
    std::ptrdiff_t step{1};
};

template <typename T>
auto range(T begin, T end, T step = 1) {
    std::vector<T> result;
    if ((step > 0 and begin < end) or (step < 0 and begin > end)) {
        for (T i = begin; (step > 0) ? (i < end) : (i > end); i += step) {
            result.push_back(i);
        }
    }
    return result;
}
template <typename T>
auto range(T end) {
    return range(T(0), end, T(1));
}
auto range(range_param param) { return range(param.begin, param.end, param.step); }
```

## chmin和chmax
更新最大/最小值
用法：if (chmax(a, b)) {...}
```cpp
bool chmin(auto& x, auto&& y) { return (x > y) ? (x = y, true) : false; }
bool chmax(auto& x, auto&& y) { return (x < y) ? (x = y, true) : false; }
```

## min
找出容器中最小值
用法：auto mn = min(a);
```cpp
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
```

## max
找出容器中最大值
用法：auto mx = max(a);
```cpp
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
```

## sum
计算容器内元素之和
用法：auto s = sum(a);
```cpp
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
```

## sub
容器切片
用法：auto b = sub(a, 1, n);
```cpp
template <typename T>
vector<T> sub(const vector<T>& vec, int start, int end) {
    int len = vec.size();
    if (start < 0) start += len;
    if (end < 0) end += len;
    if (start < 0) start = 0;
    if (end > len) end = len;
    if (start >= end) return {};

    span<const T> sp(vec);
    return {sp.begin() + start, sp.begin() + end};
}
```
## shuffled
打乱序列
用法：auto s = shuffled(a);
```cpp
auto shuffled(auto a) {
    mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
    std::ranges::shuffle(a, rng);
    return a;
}
```

## sorted
容器排序
用法：auto s = uniqued(a);
```cpp
template <typename... Args>
auto sorted(auto a, Args&&... args) {
    std::ranges::sort(a, forward<Args>(args)...);
    return a;
}
```

## uniqued
容器去重
用法：auto u = uniqued(a);
```cpp
auto uniqued(auto a) {
    std::ranges::sort(a);
    a.erase(std::unique(a.begin(), a.end()), a.end());
    return a;
}
```

## transposed
矩阵转置
用法：auto t = transposed(matrix);
```cpp
template <typename T>
T transposed(T a) {
    if (a.empty()) return a;
    std::size_t m = a.size(), n = a[0].size();
    T b(n, vector<typename T::value_type::value_type>(m));
    for (std::size_t j : range(n)) {
        for (std::size_t i : range(m)) {
            b[j][i] = a[i][j];
        }
    }
    return b;
}
```

## make_vector
创建多维向量
用法：auto vec = make_vector(3, 4, 0); // 创建3x4的二维向量，初始值为0
```cpp
template <typename T>
auto make_vector(T x) {
    return x;
}
template <typename T1, typename T2, typename... Args>
auto make_vector(T1 m, T2 n, Args... arg) {
    return vector(m, make_vector(n, arg...));
}
```
## threshold_search
二分函数
用法：wait！！！
```cpp
template <typename T, typename Pred>
auto threshold_search(T start, T end, Pred pred) {
    auto range = std::ranges::iota_view(start, end);
    return *std::ranges::partition_point(range, pred);
}
```
## print和rd
print用法：
打印元组：cout << tuple << '\n';
打印pair：cout << pair << '\n';
打印vector：cout << vector << '\n';

rd用法：
从标准输入读取数据：
auto n = rd\<int>(); 
读取多个值：auto [a, b] = rd<2, int>();
读取n个值：auto vec = rd\<int>(n);

```c++
template <typename Tuple, typename F, std::size_t... N>
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
    for (const auto& elem : v) out << elem << ' ';
    return out;
}
template <typename T, typename... Args>
void print(const T& t, const Args&... args) {
    std::cout << t;
    if constexpr (sizeof...(args)) {
        std::cout << ' ';
        print(args...);
    } else {
        std::cout << '\n';
    }
}

template <typename T = i64>
auto rd() {
    T tmp;
    std::cin >> tmp;
    return tmp;
}
template <std::size_t N, typename T = i64>
auto rd() {
    std::array<T, N> res;
    for (auto& x : res) std::cin >> x;
    return res;
}
template <typename T = i64>
auto rd(std::size_t n, std::size_t off = 0) {
    std::vector<T> v(off + n);
    for (std::size_t i : range(off, off + n)) std::cin >> v[i];
    return v;
}
```

用法：

```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n = rd();
    auto v = rd(n);
    debug(v);
    auto [xa, ya, xb, yb] = rd<4, int>();
    debug(xa, ya, xb, yb);

    auto mx = max(v), mn = min(v);
    debug(mx, mn);

    auto a = mn, b = mx;
    debug(a, b);
    chmax(a, mx), chmin(b, mn);
    debug(a, b);

    auto s = sum(v);
    debug(s);
    auto vvv = make_vector(3, 4, 5, 0LL);
    for (auto i : range(3)) {
        for (auto j : range(4)) {
            for (auto k : range(5)) {
                vvv[i][j][k] = Rand<int>(1, 50);
            }
        }
    }
    debug(vvv);
    debug(max(vvv));
    debug(min(vvv));

    auto v1 = reversed(v);
    debug(v1);
    auto v2 = sorted(v);
    debug(v2);
    v2 = shuffled(v2);
    debug(v2);

    auto v3 = sorted(v, greater<int>());
    debug(v3);
    auto v4 = make_vector(3, 3, 1LL);
    debug(v4);
    auto v5 = v4;
    for (int i : range(3)) {
        for (int j : range(3)) {
            v5[i][j] = i * 3 + j;
        }
    }
    debug(v5);
    auto v6 = transposed(v5);
    debug(v6);
    auto v7 = v3;
    for (auto i : range(v3.size())) {
        v7.emplace_back(v3[i]);
    }
    debug(v7);
    auto v8 = uniqued(v7);
    debug(v8);
    auto v9 = make_vector(3, 4, 0LL);
    for (auto i : range(3)) {
        for (auto j : range(4)) {
            v9[i][j] = i * 3 + j;
        }
    }
    debug(v9);
    auto v10 = transposed(v9);
    debug(v10);

    auto myra = range(1, 10, 2);
    set<int> st{myra.begin(), myra.end()};
    for (auto i : st) {
        debug(i);
    }
    auto it_l = st.lower_bound(2), it_r = st.lower_bound(6);
    for (auto e : std::ranges::subrange(it_l, it_r)) {
        debug(e);
    }

    return 0;
}
```

输入

```c++
6
6 1 3 2 4 5
11 22 33 44
```

输出

```c++
D:\C++\main.cpp:281: (v) = ([6, 1, 3, 2, 4, 5])
D:\C++\main.cpp:283: (xa, ya, xb, yb) = (11, 22, 33, 44)
D:\C++\main.cpp:286: (mx, mn) = (6, 1)
D:\C++\main.cpp:289: (a, b) = (1, 6)
D:\C++\main.cpp:291: (a, b) = (6, 1)
D:\C++\main.cpp:294: (s) = (21)
D:\C++\main.cpp:303: (vvv) = ([[[15, 16, 2, 38, 7], [41, 36, 28, 30, 23], [44, 40, 46, 33, 7], [12, 43, 27, 26, 25]], [[18, 18, 4, 44, 29], [13, 44, 30, 38, 10], [24, 6, 43, 22, 29], [20, 46, 20, 39, 16]], [[11, 36, 17, 47, 14], [8, 13, 5, 6, 17], [5, 4, 29, 43, 45], [34, 23, 38, 28, 32]]])
D:\C++\main.cpp:304: (max(vvv)) = (47)
D:\C++\main.cpp:305: (min(vvv)) = (2)
D:\C++\main.cpp:308: (v1) = ([5, 4, 2, 3, 1, 6])
D:\C++\main.cpp:310: (v2) = ([1, 2, 3, 4, 5, 6])
D:\C++\main.cpp:312: (v2) = ([2, 6, 5, 4, 3, 1])
D:\C++\main.cpp:315: (v3) = ([6, 5, 4, 3, 2, 1])
D:\C++\main.cpp:317: (v4) = ([[1, 1, 1], [1, 1, 1], [1, 1, 1]])
D:\C++\main.cpp:324: (v5) = ([[0, 1, 2], [3, 4, 5], [6, 7, 8]])
D:\C++\main.cpp:326: (v6) = ([[0, 3, 6], [1, 4, 7], [2, 5, 8]])
D:\C++\main.cpp:331: (v7) = ([6, 5, 4, 3, 2, 1, 6, 5, 4, 3, 2, 1])
D:\C++\main.cpp:333: (v8) = ([1, 2, 3, 4, 5, 6])
D:\C++\main.cpp:340: (v9) = ([[0, 1, 2, 3], [3, 4, 5, 6], [6, 7, 8, 9]])
D:\C++\main.cpp:342: (v10) = ([[0, 3, 6], [1, 4, 7], [2, 5, 8], [3, 6, 9]])
D:\C++\main.cpp:347: (i) = (1)
D:\C++\main.cpp:347: (i) = (3)
D:\C++\main.cpp:347: (i) = (5)
D:\C++\main.cpp:347: (i) = (7)
D:\C++\main.cpp:347: (i) = (9)
D:\C++\main.cpp:351: (e) = (3)
D:\C++\main.cpp:351: (e) = (5)
```