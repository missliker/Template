# 单调栈

[可供参考例题：洛谷单调栈](https://www.luogu.com.cn/problem/P5788)

例：给你一个序列 $a$ ，找每个下标 $i$ 左边第一个大于 $a[i]$ 的数的位置和右边第一个大于 $a[i]$ 的数的位置

```c++
vector<int> L(n + 1), R(n + 1);
stack<int> stk;
for (int i = 1; i <= n; i++) {
    while (not stk.empty() and a[stk.top()] <= a[i]) {
        stk.pop();
    }
    if (stk.empty()) {
        L[i] = 0;
    } else {
        L[i] = stk.top();
    }
    stk.emplace(i);
}
debug(L);

stk = stack<int>();
for (int i = n; i >= 1; i--) {
    while (not stk.empty() and a[stk.top()] <= a[i]) {
        stk.pop();
    }
    if (stk.empty()) {
        R[i] = n + 1;
    } else {
        R[i] = stk.top();
    }
    stk.emplace(i);
}
debug(R);
```

