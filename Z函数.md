[KMP网课链接](https://pan.baidu.com/pfile/video?path=%2F%E7%AE%97%E6%B3%95%E5%90%88%E9%9B%86%2F00093%E7%9B%9B%E9%80%9A%E9%80%9A%E4%BF%A1%E5%A5%A5c%2B%2B6%E5%A5%97%2F1.%E3%80%90%E4%BB%A3%E7%A0%81%E6%BA%90%E7%BC%96%E7%A8%8B%E3%80%91%E4%BB%A3%E7%A0%81%E6%BA%90%E5%88%9D%E7%BA%A7%E8%AF%BE%2F01.%E5%88%9D%E7%BA%A7%E8%AF%BE%E5%8C%85%E8%A1%A5%E5%85%85%E8%AF%BE%2F3.1%EF%BC%9A-kmp(2022-07-28%2012-27-52).mp4&theme=light&from=home)

# 前述：

KMP主要解决字符串匹配问题，即在字符串S中查找某个字符串P是否出现

子串是要求连续，子序列并不要求连续

我们让n为S的长度，m为P的长度

# 暴力：

对S的所有长度与P相同的子串进行枚举，观察是否一样，暴力做法的复杂度为$O(nm)$

# 优化：

我们可以用字符串哈希的办法来进行优化，使用哈希可以让复杂度变为$O(n + m)$

但是哈希并不能保证一定对！

# 正解KMP：

复杂度：$O(n + m)$

```cpp
string s, p;
cin >> s >> p;
int n = s.size(), m = p.size();
s = " " + s;
p = " " + p;
vector<int> nxt(m + 1), f(n + 1);

for (int i = 2, j = 0; i <= m; i++) {
    while (j and p[i] != p[j + 1]) j = nxt[j];
    if (p[i] == p[j + 1]) j += 1;
    nxt[i] = j;
}

for (int i = 1, j = 0; i <= n; i++) {
    while (j and s[i] != p[j + 1]) j = nxt[j];
    if (s[i] == p[j + 1]) j += 1;
    f[i] = j;
}
```



上面是最基础的KMP运用，事实上，我们可以把s和p拼接起来，然后只算一次循环



但是能保证100%正确

```cpp
int n, m;
int nxt[M + 1],f[N + 1];
char s[N + 2],p[M + 2];

void KMP() {
    n = strlen(s + 1),m = strlen(p + 1);
    int j = 0;
    nxt[1] = 0;
    for(int i = 2;i <= m;i++) {
        while(j > 0 && p[j + 1] != p[i]) j = nxt[j];
        if(p[j + 1] == p[i]) j++;
        nxt[i] = j;
    }
    j = 0;
    for(int i = 1;i <= n;i++) {
        while(j == m || j > 0 && p[j + 1] != s[i]) j = nxt[j];
        if(p[j + 1] == s[i]) j++;
        f[i] = j;
    }
}

void query() {
    for(int i = 1;i <= n;i++)
    	if(f[i] == m) cout << i - m + 1 << ' ';
}
```



# 写法优化：

```cpp
int n,m;
int nxt[2 * M + 1];
char s[N + 2],p[2 * M + 10];

inline void KMP() {
    n = strlen(s + 1),m = strlen(p + 1);
    p[m + 1] = '#';
    for(int i = 1,j = m + 2;i <= n;i++,j++) p[j] = s[i];
    int j = 0;
    nxt[1] = 0;
    for(int i = 2;i <= n + m + 1;i++) {
        while(j > 0 && p[j + 1] != p[i]) j = nxt[j];
        if(p[j + 1] == p[i]) j++;
        nxt[i] = j;
    }
}

void query() {
    for(int i = m + 2;i <= n + m + 1;i++) 
        if(nxt[i] == m) cout << i - 2 * m  << ' ';
}
```



# 注意事项：

读入的时候若采用:

```cpp
cin >> s + 1 >> p + 1;
```

则语言不要选择c++20，会出现编译失败的现象！



# 例题：

[KMP板子题](https://www.luogu.com.cn/problem/P3375)

[最小循环覆盖](https://www.luogu.com.cn/problem/P4391)



# Z函数

有两个字符串s和p，我希望知道s的每一位，往后看能与p最多能匹配多少位

![image-20240218182507177](./../../assets/image-20240218182507177.png)



![image-20240216144346216](./../../assets/image-20240216144346216.png)

```cpp
vector<int> zFunction(string b) {
    int n_ = b.size();
    vector<int> z(n_ + 1);
    z[0] = n_;
    for (int i = 1, j = 1; i < n_; i++) {
        z[i] = max(0, min(j + z[j] - i, z[i - j]));
        while (i + z[i] < n_ and b[z[i]] == b[i + z[i]]) z[i] += 1;
        if (i + z[i] > j + z[j]) j = i;
    }
    return z;
}
// auto z = zFunction(b);
```

```cpp
vector<int> zFunction(string b) {
    int n_ = b.size();
    vector<int> z(n_ + 1);
    z[0] = n_;
    for (int i = 1, j = 1; i < n_; i++) {
        z[i] = max(0, min(j + z[j] - i, z[i - j]));
        while (i + z[i] < n_ and b[z[i]] == b[i + z[i]]) z[i] += 1;
        if (i + z[i] > j + z[j]) j = i;
    }
    return z;
}
// auto z = zFunction(b);

vector<int> pFunction(string a, string b, vector<int>& z) {
    int n_ = a.size();
    int m_ = b.size();
    vector<int> p(n_ + 1);
    p[0] = n_;
    for (int i = 0, j = 1; i < n_; i++) {
        p[i] = max(0, min(j + p[j] - i, z[i - j]));
        while (p[i] < m_ and i + p[i] < n_ and b[p[i]] == a[i + p[i]]) p[i] += 1;
        if (i + p[i] > j + p[j]) j = i;
    }
    return p;
}
// auto p = pFunction(a, b, z);
```

模板题:[扩展KMP](https://www.luogu.com.cn/problem/P5410)

例题:[好字符](https://ac.nowcoder.com/acm/contest/87255/E)

$pFunction$具体用法是,给你两个字符串,我们假定长的为$a$, 短的为$b$

我们按一下方式枚举:

```cpp
for (int i = 1; i <= a.size(); i++) {
    \\ do something with p[i]
}
```

$p[i]$表示$b$字符串与$a$的后缀字符串$a.substr(i-1)$的最长公共前缀长度

