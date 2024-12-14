```c++
#include <ranges>
using i64 = long long;
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

auto reversed(auto a) {
    std::ranges::reverse(a);
    return a;
}

auto shuffled(auto a) {
    mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
    std::ranges::shuffle(a, rng);
    return a;
}

auto uniqued(auto a) {
    std::ranges::sort(a);
    a.erase(std::unique(a.begin(), a.end()), a.end());
    return a;
}

template <typename... Args>
auto sorted(auto a, Args&&... args) {
    std::ranges::sort(a, forward<Args>(args)...);
    return a;
}

template <typename T, typename Pred>
auto threshold_search(T start, T end, Pred pred) {
    auto range = std::ranges::iota_view(start, end);
    return *std::ranges::partition_point(range, pred);
}

template <typename T>
auto make_vector(T x) {
    return x;
}

template <typename T1, typename T2, typename... Args>
auto make_vector(T1 m, T2 n, Args... arg) {
    return vector(m, make_vector(n, arg...));
}

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

