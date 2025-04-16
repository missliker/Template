# 字典树



```cpp
int idx = 1;
const int N = 1e6 + 10;
vector<vector<int>> trie(N, vector<int> (65));
vector<int> cnt(N);

auto add = [&](string s) {
    int p = 1;
    for (int i = 0; i < s.size(); i++) {
        int &now = trie[p][s[i] - 'a'];
        if (!now) now = ++idx;
        p = now;
    }
    cnt[p]++;
};

auto query = [&](string s) {
    int p = 0;
    for (int i = 0; i < s.size(); i++) {
        int &now = trie[p][s[i] - 'a'];
        if (!now) return 0;
        p = now;
    }
    return cnt[p];
};

//不过要小心vector背刺（
```



模板题：[模板：字典树](https://www.luogu.com.cn/problem/P8306)

```cpp
#include <bits/stdc++.h>

using namespace std;
using i64 = int64_t;

map<char, int> mp;
int tot;
// int idx;
// int son[3000010][65];
// int cnt[3000010];

void solve() {
    int n, q;
    cin >> n >> q;

    int idx = 0;
    vector<vector<int>> son(3000010, vector<int> (65));
    vector<int> cnt(3000010);

    auto add = [&](string s) {
        int p = 0;
        for (int i = 0; i < s.size(); i++) {
            int u = mp[s[i]];
            if (!son[p][u]) son[p][u] = ++idx;
            p = son[p][u];
            cnt[p]++;
        }
    };

    auto query = [&](string s) {
        int p = 0;
        for (int i = 0; i < s.size(); i++) {
            int u = mp[s[i]];
            if (!son[p][u]) return 0;
            p = son[p][u];
        }
        return cnt[p];
    };

    // for (int i = 0; i <= idx; i++) {
    //     cnt[i] = 0;
    //     for (int j = 0; j < 65; j++) son[i][j] = 0;
    // }
    // idx = 0;

    for (int i = 1; i <= n; i++) {
        string s;
        cin >> s;
        add(s);
    }
    while (q --) {
        string s;
        cin >> s;
        cout << query(s) << "\n";
    }
}

int main() {
    cin.tie(nullptr) ->
    sync_with_stdio(false);
    cout << fixed << setprecision(6);
    int T = 1;
    cin >> T;
    for (char i = 'a'; i <= 'z'; i++) mp[i] = ++tot;
    for (char i = 'A'; i <= 'Z'; i++) mp[i] = ++tot;
    for (char i = '0'; i <= '9'; i++) mp[i] = ++tot;
    while (T --) solve();
    return 0;
}
```



# CF练习题(1900)：

[Collapsing Strings](https://codeforces.com/contest/1902/problem/E)

```cpp
#include <bits/stdc++.h>

using namespace std;
using i64 = int64_t;

int idx = 1;
const int N = 1e6 + 10;
vector<vector<int>> son(N, vector<int> (26));
vector<int> cnt(N);

int main() {
	cin.tie(nullptr) ->
	sync_with_stdio(false);
	cout << fixed << setprecision(6);

	int n;
	cin >> n;
	vector<string> s(n);

	i64 ans = 0;
	for (int i = 0; i < n; i++) {
		cin >> s[i];
		ans += s[i].size();
		int p = 1;
		for (auto c : s[i]) {
			if (!son[p][c - 'a']) son[p][c - 'a'] = ++idx;
			p = son[p][c - 'a'];
			cnt[p] += 1;
		}
	}
	ans = ans * 2 * n;
	for (int i = 0; i < n; i++) {
		reverse(s[i].begin(), s[i].end());
		int p = 1;
		for (auto c : s[i]) {
			if (!son[p][c - 'a']) break;
			p = son[p][c - 'a'];
			ans -= 2 * cnt[p];
		}
	}
	cout << ans << "\n";

	return 0;
}
```

思路就是正向建树，反向减重叠部分*2，感觉没见过的话，赛时写可能想不到字典树，更可能往字符串hash上想？感觉用字典树很妙，也应该是正解。
