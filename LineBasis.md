# 线性基

线性基是一个数的集合。每个序列都拥有至少一个线性基。取线性基中若干个数异或起来可以得到原序列中的任何一个数。

## 性质一

原序列的任意一个数都可以由线性基内部的一些数异或得到。

## 性质二

线性基内部的任意数异或起来都不能得到0。

## 性质三

线性基内部的数个数唯一；且在保持性质一的前提下，数的个数是最少的。



[洛谷例题](https://www.luogu.com.cn/problem/P3812)

集合的最大异或和

高斯消元构造线性基

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

    int n;
    cin >> n;
    vector<i64> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }
    int m = 0;
    for (int i = 62; i >= 0; i--) {
        for (int j = m; j < n; j++) {
            if (a[j] >> i & 1) {
                swap(a[j], a[m]);
                break;
            }
        }
        if ((a[m] >> i & 1) == 0) {
            continue;
        }
        for (int j = 0; j < n; j++) {
            if (j != m and (a[j] >> i & 1)) {
                a[j] ^= a[m];
            }
        }
        m++;
        if (m == n) {
            break;
        }
    }

    i64 ans = 0;
    for (int i = 0; i < m; i++) {
        ans ^= a[i];
    }
    cout << ans << '\n';

    return 0;
}
```



集合的第k小异或和

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

    int n;
    cin >> n;
    vector<i64> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }
    int m = 0;
    for (int i = 62; i >= 0; i--) {
        for (int j = m; j < n; j++) {
            if (a[j] >> i & 1) {
                swap(a[j], a[m]);
                break;
            }
        }
        if ((a[m] >> i & 1) == 0) {
            continue;
        }
        for (int j = 0; j < n; j++) {
            if (j != m and (a[j] >> i & 1)) {
                a[j] ^= a[m];
            }
        }
        m++;
        if (m == n) {
            break;
        }
    }

    int k;
    cin >> k;
    if (m < n) {
        k--;
    }
    
    i64 ans = 0;
    if (k >= (1LL << m)) {
        ans = -1;
    } else {
        for (int i = 0; i < m; i++) {
            if (k >> i & 1) {
                ans ^= a[k - 1 - i];
            }
        }
    }
    cout << ans << '\n';

    return 0;
}
```



# missliker version

```c++
struct Basis {
    i64 w[63] = {};
    int m = 0;

    void assign() {
        for (int i = 0; i < m; i++) {
            w[i] = 0;
        }
        m = 0;
    }

    bool insert(i64 x) {
        for (int i = 62; i >= 0; i--) {
            if (x >> i & 1) {
                if (not w[i]) {
                    w[i] = x;
                    if (i + 1 > m) {
                        m = i + 1;
                    }
                    return true;
                }
                x ^= w[i];
            }
        }
        return false;
    }

    void remake(int n) {
        m = 0;
        for (int i = 62; i >= 0; i--) {
            for (int j = m; j < n; j++) {
                if (w[j] >> i & 1) {
                    swap(w[j], w[m]);
                    break;
                }
            }
            if ((w[m] >> i & 1) == 0) {
                continue;
            }
            for (int j = 0; j < n; j++) {
                if (j != m and (w[j] >> i & 1)) {
                    w[j] ^= w[m];
                }
            }
            m++;
            if (m == n) {
                break;
            }
        }
        sort(w, w + m);
    }

    i64 max() {
        i64 res = 0;
        for (int i = m - 1; i >= 0; i--) {
            if ((res ^ w[i]) > res) {
                res ^= w[i];
            }
        }
        return res;
    }

    i64 min() { return w[0]; }

    i64 Kth_min(int k, int n) {
        if (m < n) {
            k--;
        }
        if (k >= (1LL << m)) {
            return -1;
        }
        i64 res = 0;
        for (int i = 0; i < m; i++) {
            if (k >> i & 1) {
                res ^= w[i];
            }
        }
        return res;
    }

    int size() { return m; }
};
```



# jly version

```c++
struct Basis {
    int a[20]{};
    int t[20]{};

    Basis() { std::fill(t, t + 20, -1); }

    void insert(int x, int y = 1E9) {
        for (int i = 0; i < 20; i++) {
            if (x >> i & 1) {
                if (y > t[i]) {
                    std::swap(a[i], x);
                    std::swap(t[i], y);
                }
                x ^= a[i];
            }
        }
    }

    bool query(int x, int y = 0) {
        for (int i = 0; i < 20; i++) {
            if ((x >> i & 1) && t[i] >= y) {
                x ^= a[i];
            }
        }
        return x == 0;
    }
} basis;
```

