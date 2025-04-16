# 字符串哈希

复杂度：

预处理： $O(n)$

查询： $O(1)$

## 模板

在不带修的区间查询子串的时候可以达到O(1)的效果

```c++
bool isprime(int n) {
    if (n <= 1) return false;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}

int findPrime(int n) {
    while (!isprime(n)) n++;
    return n;
}

using Pair = pair<i64, i64>;
struct Hash {
    const int Base1 = 31, Base2 = 37;
    static int Mod1, Mod2;
    vector<i64> Hash1, Hash2, Power1, Power2;
    
    Hash() {}

    Hash(const string& str) {
        int N = str.size();
        Hash1.assign(N + 1, 0);
        Hash2.assign(N + 1, 0);
        Power1.assign(N + 1, 1);
        Power2.assign(N + 1, 1);

        for (int i = 1; i <= N; i++) {
            Hash1[i] = (Hash1[i - 1] * Base1 + str[i - 1]) % Mod1;
            Hash2[i] = (Hash2[i - 1] * Base2 + str[i - 1]) % Mod2;
            Power1[i] = (Power1[i - 1] * Base1) % Mod1;
            Power2[i] = (Power2[i - 1] * Base2) % Mod2;
        }
    }

    Pair get(int l, int r) {
        i64 hash1 = ((Hash1[r] - Hash1[l - 1] * Power1[r - l + 1]) % Mod1 + Mod1) % Mod1;
        i64 hash2 = ((Hash2[r] - Hash2[l - 1] * Power2[r - l + 1]) % Mod2 + Mod2) % Mod2;
        return {hash1, hash2};
    }
};

mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
int Hash::Mod1 = findPrime(rng() % 900000000 + 100000000);
int Hash::Mod2 = findPrime(rng() % 900000000 + 100000000);
```

## 用法

```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    string s;
    cin >> s;
    int n = s.size();

    Hash h(s);
    auto ans = h.get(1, n);
    debug(ans);

    return 0;
}
```

想把N放在构造函数外面，看看后面行不行
