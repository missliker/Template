# 初始化模板

## 单组数据in

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

struct range_param {
    std::size_t begin{};
    std::size_t end{};
    std::size_t step{1};
};
auto range(std::size_t begin, std::size_t end, std::size_t step = 1) { return views::iota(begin, end) | views::stride(step); }
auto range(std::size_t end) { return views::iota(0UZ, end) | views::stride(1); }
auto range(range_param param) { return range(param.begin, param.end, param.step); }

bool chmax(auto& x, auto&& y) { return (x < y) ? (x = y, true) : false; }
bool chmin(auto& x, auto&& y) { return (x > y) ? (x = y, true) : false; }

template <typename T>
auto make_vector(T x) {
    return x;
}
template <typename T1, typename T2, typename... Args>
auto make_vector(T1 m, T2 n, Args... arg) {
    return vector(m, make_vector(n, arg...));
}

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

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    ${1:}

    return 0;
}
```

## 多组数据ins

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

struct range_param {
    std::size_t begin{};
    std::size_t end{};
    std::size_t step{1};
};
auto range(std::size_t begin, std::size_t end, std::size_t step = 1) { return views::iota(begin, end) | views::stride(step); }
auto range(std::size_t end) { return views::iota(0UZ, end) | views::stride(1); }
auto range(range_param param) { return range(param.begin, param.end, param.step); }

bool chmax(auto& x, auto&& y) { return (x < y) ? (x = y, true) : false; }
bool chmin(auto& x, auto&& y) { return (x > y) ? (x = y, true) : false; }

template <typename T>
auto make_vector(T x) {
    return x;
}
template <typename T1, typename T2, typename... Args>
auto make_vector(T1 m, T2 n, Args... arg) {
    return vector(m, make_vector(n, arg...));
}

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

void Solution(int curCase) {
    ${1:}
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int maxCase = 1;
    cin >> maxCase;
    for (int curCase = 1; curCase <= maxCase; curCase++) {
        Solution(curCase);
    }

    return 0;
}
```