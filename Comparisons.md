# 对拍

## run.bat
```bash
g++ A__Generator.cpp -o A__Generator -std=c++23 -O2 -g
g++ A.cpp -o A -std=c++23 -O2 -g
g++ A__Good.cpp -o A__Good -std=c++23 -O2 -g

:loop
    A__Generator.exe > A__Generator.in
    A.exe < A__Generator.in > A.out
    A__Good.exe < A__Generator.in > A__Good.out
    fc A.out A__Good.out
if not errorlevel 1 goto loop
pause
goto loop
```

## remove.bat
```bash
del A__Generator.exe
del A.exe
del A__Good.exe
del A__Generator.in
del A.out
del A__Good.out
del run.exe
del diff.log
```

## A__Generator.cpp
```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using d64 = long double;
using Pair = pair<i64, i64>;
mt19937 RandSeed(chrono::steady_clock::now().time_since_epoch().count());

i64 intRand(i64 mod) { return RandSeed() * RandSeed() % mod; }
i64 intRand(i64 l, i64 r) { return l + intRand(r - l + 1); }
d64 doubleRand(d64 l, d64 r) {
    uniform_real_distribution<d64> dis(l, r);
    d64 res = dis(RandSeed);
    return res;
}
char smallCharRand() { return char('a' + intRand(26)); }
char bigCharRand() { return char('A' + intRand(26)); }

string smallStringRand(i64 n) {
    string res;
    for (int i = 0; i < n; i++) {
        res += smallCharRand();
    }
    return res;
}
string bigStringRand(i64 n) {
    string res;
    for (int i = 0; i < n; i++) {
        res += bigCharRand();
    }
    return res;
}
vector<i64> vectorRand(i64 n, i64 l, i64 r) {
    vector<i64> res;
    for (int i = 0; i < n; i++) {
        i64 x = intRand(l, r);
        res.emplace_back(x);
    }
    return res;
}

vector<Pair> treeRand(i64 n) {
    vector<Pair> tree;
    for (int i = 2; i <= n; i++) {
        i64 u = i, v = intRand(1, i - 1);
        tree.emplace_back(u, v);
    }
    return tree;
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);
    // freopen("./Hacker.in", "w", stdout);

    // srand(time(nullptr));
    // random_shuffle(v.begin(), v.end());

    int t = 1;
    // cout << t << endl;
    while (t--) {
        $0
    }
    
    return 0;
}
```

## run.cpp
```c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    system("g++ A__Generator.cpp -o A__Generator -std=c++20 -O2 -g");
    system("g++ A.cpp -o A -std=c++20 -O2 -g");
    system("g++ A__Good.cpp -o A__Good -std=c++20 -O2 -g");

    int t = 0;
    while (true) {
        cout << "test: " << t++ << endl;
        system("A__Generator.exe > A__Generator.in");
        system("A.exe < A__Generator.in > A.out");
        system("A__Good.exe < A__Generator.in > A__Good.out");
        if (system("fc A.out A__Good.out > diff.log")) {
            cout << "WA\n";
            break;
        }
        cout << "AC\n";
    }
    return 0;
}
```