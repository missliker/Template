# generator.h

> [!Tip]
>头文件默认存放在debug.h中

```c++
#pragma once
#include <bits/stdc++.h>
using i64 = long long;
using d32 = double;
using d64 = long double;
using Pair = std::pair<i64, i64>;
std::mt19937 rng(std::chrono::steady_clock::now().time_since_epoch().count());

int intRand(int mod) { return (1LL * rng() + rng()) % mod; }

i64 intRand(int l, int r) { return l + intRand(r - l + 1); }

i64 i64Rand(i64 mod) { return (rng() << 15LL | rng()) % mod; }

i64 i64Rand(i64 l, i64 r) { return l + i64Rand(r - l + 1); }

d64 d64Rand(d64 l, d64 r) {
    std::uniform_real_distribution<d64> dist(l, r + 1e-15);
    return dist(rng);
}

char smallCharRand() { return char('a' + i64Rand(0, 25)); }

char bigCharRand() { return char('A' + i64Rand(0, 25)); }

char numberCharRand() { return char('0' + i64Rand(0, 9)); }

char charRand(const std::string& type) {
    if (type == "small") return smallCharRand();
    if (type == "big") return bigCharRand();
    if (type == "number") return numberCharRand();
    return ' ';
}

std::string smallStringRand(i64 n) {
    std::string res;
    for (int i = 0; i < n; i++) res += smallCharRand();
    return res;
}

std::string bigStringRand(i64 n) {
    std::string res;
    for (int i = 0; i < n; i++) res += bigCharRand();
    return res;
}

std::string numberStringRand(i64 n) {
    std::string res;
    for (int i = 0; i < n; i++) res += numberCharRand();
    return res;
}

std::string otherStringRand(i64 n) {
    std::string res;
    for (int i = 0; i < n; i++) {
        int o = i64Rand(1, 4);
        if (o == 1) res += numberCharRand();
        else if (o == 2) res += smallCharRand();
        else if (o == 3) res += bigCharRand();
        else res += ' ';
    }
    return res;
}

std::string stringRand(i64 n, const std::string& type) {
    if (type == "small") return smallStringRand(n);
    if (type == "big") return bigStringRand(n);
    if (type == "number") return numberStringRand(n);
    return otherStringRand(n);
}

template <typename T>
T Rand() {
    if constexpr (std::is_same_v<T, char>) return charRand("small");
    if constexpr (std::is_same_v<T, int>) return i64Rand(0, 998244353);
    if constexpr (std::is_same_v<T, i64>) return i64Rand(0, i64(1E18) + 114514);
    if constexpr (std::is_same_v<T, d32>) return d64Rand(0, 100);
    if constexpr (std::is_same_v<T, d64>) return d64Rand(0, 100);
    if constexpr (std::is_same_v<T, std::string>) return stringRand(i64Rand(1, 10), "small");
}

template <typename T>
std::enable_if_t<std::is_same_v<T, int> or std::is_same_v<T, i64> or std::is_same_v<T, d32> or std::is_same_v<T, d64>, T> Rand(T l, T r) {
    if constexpr (std::is_same_v<T, int>) return i64Rand(l, r);
    if constexpr (std::is_same_v<T, i64>) return i64Rand(l, r);
    if constexpr (std::is_same_v<T, d32>) return d64Rand(l, r);
    if constexpr (std::is_same_v<T, d64>) return d64Rand(l, r);
    return T();
}

template <typename T>
std::enable_if_t<std::is_same_v<T, char>, T> Rand(const std::string& type) {
    return charRand(type);
}

template <typename T>
std::enable_if_t<std::is_same_v<T, std::string>, T> Rand(i64 n, const std::string& type) {
    return stringRand(n, type);
}

template <typename T>
std::enable_if_t<std::is_same_v<T, std::vector<char>>, T> Rand(i64 n, const std::string& type) {
    T res;
    for (i64 i = 0; i < n; i++) res.emplace_back(charRand(type));
    return res;
}

template <typename T>
std::enable_if_t<std::is_same_v<T, std::vector<std::string>>, T> Rand(i64 n, i64 sz, const std::string& type) {
    T res;
    for (i64 i = 0; i < n; i++) res.emplace_back(stringRand(Rand<i64>(1, sz), type));
    return res;
}

template <typename T>
std::enable_if_t<std::is_same_v<T, char> or std::is_same_v<T, int> or std::is_same_v<T, i64> or std::is_same_v<T, d32> or std::is_same_v<T, d64> or std::is_same_v<T, std::string>, std::vector<T>>
Rand(i64 n) {
    std::vector<T> res;
    for (i64 i = 0; i < n; i++) res.emplace_back(Rand<T>());

    return res;
}

template <typename T>
std::enable_if_t<std::is_same_v<T, std::vector<char>> or std::is_same_v<T, std::vector<int>> or std::is_same_v<T, std::vector<i64>> or std::is_same_v<T, std::vector<d32>> or
                     std::is_same_v<T, std::vector<d64>> or std::is_same_v<T, std::vector<std::string>>,
                 T>
Rand(i64 n) {
    T res;
    for (i64 i = 0; i < n; i++) res.emplace_back(Rand<typename T::value_type>());
    return res;
}

template <typename T>
std::enable_if_t<std::is_same_v<T, char> or std::is_same_v<T, int> or std::is_same_v<T, i64> or std::is_same_v<T, d32> or std::is_same_v<T, d64> or std::is_same_v<T, std::string>, std::vector<T>>
Rand(i64 n, T l, T r) {
    std::vector<T> res;
    for (i64 i = 0; i < n; i++) res.emplace_back(Rand<T>(l, r));
    return res;
}

template <typename T>
std::enable_if_t<std::is_same_v<T, std::vector<char>> or std::is_same_v<T, std::vector<int>> or std::is_same_v<T, std::vector<i64>> or std::is_same_v<T, std::vector<d32>> or
                     std::is_same_v<T, std::vector<d64>> or std::is_same_v<T, std::vector<std::string>>,
                 T>
Rand(i64 n, typename T::value_type l, typename T::value_type r) {
    T res;
    for (i64 i = 0; i < n; i++) res.emplace_back(Rand<typename T::value_type>(l, r));
    return res;
}

std::vector<Pair> treeRand(i64 n) {
    std::vector<Pair> tree;
    for (int i = 2; i <= n; i++) tree.emplace_back(i, Rand<int>(1, i - 1));
    return tree;
}

std::vector<Pair> graphRand(i64 n) {
    std::vector<int> topo(n);
    std::vector<Pair> graph;
    for (int i = 0; i < n; i++) topo[i] = i;
    shuffle(topo.begin(), topo.end(), rng);
    // 遍历每一对节点(i, j)，如果i < j，可能添加一条边
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            // 在拓扑排序中保证边的方向
            int u = topo[i];
            int v = topo[j];
            // 以概率 p 添加一条边(u, v)
            if ((Rand<int>(0, std::numeric_limits<short>::max()) / double(std::numeric_limits<short>::max())) < 0.5) {
                graph.emplace_back(u, v);
            }
        }
    }
    return graph;
}
```

用法
```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    auto i = Rand<i64>();
    debug(i);
    auto i2 = Rand<i64>(1, 120);
    debug(i2);
    auto d = Rand<d64>();
    debug(d);
    auto d2 = Rand<d64>(12, 100.0);
    debug(d2);
    auto s = Rand<string>();
    debug(s);
    auto s2 = Rand<string>(25, "big");
    debug(s2);
    auto s3 = Rand<string>(i2, "other");
    debug(s3);

    int n = 6, m = 20, l = 1, r = 100;
    debug(n, m);
    auto v1 = Rand<vector<int>>(n);
    debug(v1);
    auto v2 = Rand<vector<int>>(n, l, r);
    debug(v2);
    auto v3 = Rand<vector<i64>>(n);
    debug(v3);
    auto v4 = Rand<vector<i64>>(n, 3, 234);
    debug(v4);
    auto v5 = Rand<vector<char>>(n);
    debug(v5);
    auto v6 = Rand<vector<char>>(n, "big");
    debug(v6);
    auto v7 = Rand<vector<string>>(n);
    debug(v7);
    auto v8 = Rand<vector<string>>(n, m, "number");
    debug(v8);

    auto tree = treeRand(n);
    debug(n);
    for (auto& [u, v]: tree) {
        debug(u, v);
    }

    debug();

    auto graph = graphRand(n);
    m = graph.size();
    debug(n, m);
    for (auto& [u, v] : graph) {
        debug(u, v);
    }

    debug(std::numeric_limits<short>::max());
    debug(RAND_MAX);

    return 0;
}
```

输出
```c++
D:\C++\main.cpp:16: (i) = (4135492039)
D:\C++\main.cpp:18: (i2) = (7)
D:\C++\main.cpp:20: (d) = (49.99612785149524022896)
D:\C++\main.cpp:22: (d2) = (16.25282088628535063883)
D:\C++\main.cpp:24: (s) = (pzde)
D:\C++\main.cpp:26: (s2) = (NAHJFBLLODTCTABFSXNEVCQRI)
D:\C++\main.cpp:28: (s3) = (  9Axt5)
D:\C++\main.cpp:31: (n, m) = (6, 20)
D:\C++\main.cpp:33: (v1) = ([678105752, 75485252, 944473554, 134141252, 626936518, 157210080])
D:\C++\main.cpp:35: (v2) = ([13, 43, 11, 43, 37, 43])
D:\C++\main.cpp:37: (v3) = ([947910433, 2010030779, 1610163612, 2381299406, 2835325639, 4227781962])
D:\C++\main.cpp:39: (v4) = ([157, 13, 124, 5, 5, 7])
D:\C++\main.cpp:41: (v5) = ([v, h, s, g, n, g])
D:\C++\main.cpp:43: (v6) = ([Q, V, A, M, V, R])
D:\C++\main.cpp:45: (v7) = ([ayivmckjp, cmsyzviiak, goxxvmtdlg, hql, i, wd])
D:\C++\main.cpp:47: (v8) = ([47484083, 8658717891, 6190983945, 699410425096, 7545112263705352, 336838366729088407])
D:\C++\main.cpp:50: (n) = (6)
D:\C++\main.cpp:52: (u, v) = (2, 1)
D:\C++\main.cpp:52: (u, v) = (3, 2)
D:\C++\main.cpp:52: (u, v) = (4, 2)
D:\C++\main.cpp:52: (u, v) = (5, 1)
D:\C++\main.cpp:52: (u, v) = (6, 1)
D:\C++\main.cpp:55: () = ()
D:\C++\main.cpp:59: (n, m) = (6, 9)
D:\C++\main.cpp:61: (u, v) = (4, 5)
D:\C++\main.cpp:61: (u, v) = (4, 3)
D:\C++\main.cpp:61: (u, v) = (4, 1)
D:\C++\main.cpp:61: (u, v) = (5, 3)
D:\C++\main.cpp:61: (u, v) = (5, 0)
D:\C++\main.cpp:61: (u, v) = (2, 3)
D:\C++\main.cpp:61: (u, v) = (2, 0)
D:\C++\main.cpp:61: (u, v) = (3, 0)
D:\C++\main.cpp:61: (u, v) = (1, 0)
D:\C++\main.cpp:64: (std::numeric_limits<short>::max()) = (32767)
D:\C++\main.cpp:65: (RAND_MAX) = (32767)
```