# 二分

[砍树](https://www.luogu.com.cn/problem/P1873)

```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = int64_t;

template <typename T, typename Pred>
auto threshold_search(T start, T end, Pred pred) {
    auto range = std::ranges::iota_view(start, end);
    return *std::ranges::partition_point(range, pred);
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int N, M;
    cin >> N >> M;
    vector<int> A(N);
    for (auto& x : A) cin >> x;

    auto ans = threshold_search(0, int(4E5 + 10), [&](int x) {
        i64 sum = 0;
        for (auto& i : A) {
            if (i > x) sum += i - x;
        }
        return sum >= M;
    });
    cout << ans - 1 << '\n';

    return 0;
}
```

