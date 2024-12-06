# 杂项

## GNU C++ 版本测试

```c++
for (int i : {1, 2}) {} // GNU C++11 支持范围表达式

auto cc = [&](int x) { x++; }; // GNU C++11 支持 auto 与 lambda 表达式
cc(2);

tuple<string, int, int> V; // GNU C++11 引入
array<int, 3> C; // GNU C++11 引入

auto dfs = [&](auto self, int x) -> void { // GNU C++14 支持 auto 自递归
    if (x > 10) return;
    self(self, x + 1);
};
dfs(dfs, 1);

vector in(1, vector<int>(1)); // GNU C++17 支持 vector 模板类型缺失

map<int, int> dic;
for (auto [u, v] : dic) {} // GNU C++17 支持 auto 解绑
dic.contains(12); // GNU C++20 支持 contains 函数

constexpr double Pi = numbers::pi; // C++20 支持

ranges::sort(vec); // C++23支持
```



## 日期换算（基姆拉尔森公式）

已知年月日，求星期数。

```cpp
int week(int y, int m, int d) {
    if (m <= 2) {
        m += 12, y--;
    }
    return (d + 2 * m + 3 * (m + 1) / 5 + y + y / 4 - y / 100 + y / 400) % 7 + 1;
}
```



## 约瑟夫问题

n 个人编号 0,1,2…,n−1 ，每次数到 k 出局，求最后剩下的人的编号。

如果编号从1开始，则res++。

O(N) 。

```cpp
int jos(int n, int k) {
    int res = 0;
    for (int i = 1; i <= n; i++) {
        res = (res + k) % i;
    }
    return res;
}
```

O(Klog⁡N) ，适用于 K 较小的情况。

```cpp
int jos(int n, int k) {
    if (n == 1 or k == 1) {
        return n - 1;
    }
    if (k > n) {
        return (jos(n - 1, k) + k) % n;
    }
    int res = jos(n - n / k, k) - n % k;
    if (res < 0) {
        res += n;
    } else {
        res += res / (k - 1);
    }
    return res;
}
```



## 有用的函数

```cpp
// 可以将一个字符串s当作16进制数，然后返回他的十进制数
string s = "123456", s2 = "12345987a45";
int ans = stoi(s, 0, 16);
i64 ans2 = stoll(s2, 0LL, 16);
```

```cpp
// i64类型的最大值
numeric_limits<i64>::max();

// i64类型的最小正数
numeric_limits<i64>::min();

// i64类型的最小值
numeric_limits<i64>::lowest();
```

```cpp
// 求和函数
i64 sum = reduce(v.begin(), v.end());
i64 sum = reduce(v.begin(), v.end(), 10LL);

i64 sum = reduce(v.begin(), v.end(), 1LL, [&](const i64& sum, const int& x) { return sum * x; });

// 推荐用reduce
i64 sum = accumulate(v.begin(), v.end(), 0LL);
```

```cpp
// 构造差分
vector<int> a(n), b(n);
adjacent_difference(a.begin(), a.end(), b.begin());
```

```cpp
// rotate
// 将[a.begin(),a.begin() + 2)这部分 和 
// [a.begin() +  2,a.begin() + 4)这部分交换位置
vector<int> a{1, 2, 3, 4, 5};
rotate(a.begin(), a.begin() + 2, a.begin() + 4);
for (auto x : a) cout << x << ' ';
// {3, 4, 1, 2, 5}
```

```cpp
// 求以2为底的log2(x),返回值是int
int res = __lg(x);

// 求以2为底的log2(x),返回值是double
double res = log2(x);

// 求以自然对数e为底的log(x),返回值是double
double res = log(x);
```

```cpp
// 最后一个元素
*a.rbegin()
a.back()
```

```cpp
// partial_sum,前缀和
vector<int> a(10), b(10); // 要先保证里面是有存储空间的
iota(a.begin(), a.end(), 1); // 生成[1, 10)

partial_sum(a.begin(), a.end(), b.begin());
copy(b.begin(), b.end(), ostream_iterator<int>(cout, " "));
// 输出1, 3, 6, 10, 15, 21, 28, 36, 45, 55
```

```cpp
vector<int> a(10); // 要先保证里面是有存储空间的
iota(a.begin(), a.end(), 1); // 生成[1, 10)
random_shuffle(a.begin(), a.end()); // 随机打乱
copy(a.begin(), a.end(), ostream_iterator<int>(cout, " "));
```

```cpp
// nth_element,可以找到第k小的数
// 前面的都比a[k]小，后面的都比a[k]大，但不保证顺序
// 最小的是第0小
int k;
cin >> k;
vector<int> a{100, 200, 300, 400, 500};
random_shuffle(a.begin(), a.end());
nth_element(a.begin(), a.begin() + k, a.end());
cout << a[k];
```

```c++
// 1 + log2(x)
int log = bit_width(x); // x为无符号整数类型（unsigned int、unsigned long、unsigned long long 或扩展无符号整数类型）
```





## 判断闰年

```cpp
auto get = [&](int x) {
    if (x % 4 == 0 and x % 100 != 0 or x % 400 == 0) return 366;
    return 365;
};
```



## 天数遍历

year1-month1-day1~year2-month2-day2

例如: 1897-7-12~2004-12-21

```cpp
vector<int> day_of_{0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
auto get = [&](int x) {
    if (x % 4 == 0 and x % 100 != 0 or x % 400 == 0) return 366;
    return 365;
};

for (int month = month1 + 1; month <= 12; month++) {
    int maxday = day_of_[month];
    if (month == 2 and get(year1) == 366) maxday++;
    for (int day = 1; day <= maxday; day++) {
        //
    }
}

for (int day = day1; day <= day_of_[month1]; day++) {
    //
}

for (int year = year1 + 1; year < year2; year++) {
    for (int month = 1; month <= 12; month++) {
        int maxday = day_of_[month];
        if (month == 2 and get(year) == 366) maxday++;
        for (int day = 1; day <= maxday; day++) {
            //
        }
    }
}

for (int month = 1; month < month2; month++) {
    int maxday = day_of_[month];
    if (month == 2 and get(year2) == 366) maxday++;
    for (int day = 1; day <= maxday; day++) {
        //
    }
}

for (int day = 1; day <= day2; day++) {
    //
}
```



## 文件

```cpp
// 读写
freopen("D:/Code/data.in", "r", stdin);
freopen("D:/Code/data.out", "w", stdout);

// 随机
srand(time(nullptr));

// 定义
ofstream ofs;

// 打开
ofs.open("data.in", ios::out);

// 把n和endl输入到data里面
ofs << n << endl;

// 最后要关闭文件
ofs.close();
```



## 代码运行时间

```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

int main() {
    clock_t Time = clock();
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int a, b;
    cin >> a >> b;
    int ans = 0;
    for (int i = 0; i < a; i++) ans++;
    for (int i = 0; i < b; i++) ans++;
    cout << ans << '\n';

    cerr << "Time Used: " << clock() - Time << "ms" << endl;
    return 0;
}
```







## Python常用语法

### 读入与定义

-   读入多个变量并转换类型：`X, Y = map(int, input().split())`
-   读入列表：`X = eval(input())`
-   多维数组定义：`X = [[0 for j in range(0, 100)] for i in range(0, 200)]`

### 格式化输出

-   保留小数输出：`print("{:.12f}".format(X))` 指保留 12 位小数
-   对齐与宽度：`print("{:<12f}".format(X))` 指左对齐，保留 12 个宽度

### 排序

-   倒序排序：使用 `reverse` 实现倒序 `X.sort(reverse=True)`

-   自定义排序：下方代码实现了先按第一关键字降序、再按第二关键字升序排序。

    ```python
    X.sort(key=lambda x: x[1])
    X.sort(key=lambda x: x[0], reverse=True)
    ```

### 文件IO

-   打开要读取的文件：`r = open('X.txt', 'r', encoding='utf-8')`
-   打开要写入的文件：`w = open('Y.txt', 'w', encoding='utf-8')`
-   按行写入：`w.write(XX)`

### 增加输出流长度、递归深度

```python
import sys
sys.set_int_max_str_digits(200000)
sys.setrecursionlimit(100000)
```

### 自定义结构体

自定义结构体并且自定义排序

```python
class node:
    def __init__(self, A, B, C):
        self.A = A
        self.B = B
        self.C = C

w = []
for i in range(1, 5):
    a, b, c = input().split()
    w.append(node(a, b, c))
w.sort(key=lambda x: x.C, reverse=True)
for i in w:
    print(i.A, i.B, i.C)
```

### 数据结构

-   模拟于 C++map ，定义：`dic = dict()`
-   模拟栈与队列：使用常见的 list 即可完成，`list.insert(0, X)` 实现头部插入、`list.pop()` 实现尾部弹出、`list.pop(0)` 实现头部弹出

### 其他

-   获取ASCII码：`ord()` 函数
-   转换为ASCII字符：`chr()` 函数