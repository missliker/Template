# 值域桶

例：给你一个数组 $a$ ，找每个下标 $i$ 左边第一次出现 $a[i]$ 的位置和右边第一次出现 $a[i]$ 的位置

```c++
vector<int> a(n + 1);
vector id(n + 1, vector<int>(1, 0));
for (int i = 1; i <= n; i++) {
    cin >> a[i];
    id[a[i]].emplace_back(i);
}

debug(id);

vector<int> lst(n + 1), nxt(n + 1, -1);
for (int i = 1; i <= n; i++) {
    if (id[i].size() < k + 1) {
        continue;
    }
    for (int j = 1; j < id[i].size(); j++) {
        lst[id[i][j]] = id[i][j - 1];
    }
    for (int j = 2; j < id[i].size(); j++) {
        nxt[id[i][j - 1]] = id[i][j];
    }
}
debug(lst);
debug(nxt);
```

