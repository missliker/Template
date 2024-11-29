# 快读快写

## 封装超快读

```c++
template <size_t S>
struct Scanner {
    char buf[S], *l = buf, *r = buf;
    bool flush() {
        l = buf;
        r = l + fread(buf, 1, S, stdin);
        return l == r;
    }
    void get(char& c) { c = l == r && flush() ? ' ' : *l++; }
    friend Scanner& operator>>(Scanner& in, char& c) { return in.get(c), in; }
    friend Scanner& operator>>(Scanner& in, char* s) {
        for (in.get(s[0]); isspace(s[0]); in.get(s[0]));
        for (int i = 0; !isspace(s[i]) || (s[i] = '\0'); i++) in.get(s[i + 1]);
        return in;
    }
    friend Scanner& operator>>(Scanner& in, std::string& s) {
        char c;
        for (in.get(c); isspace(c); in.get(c));
        for (s = ""; !isspace(c); in.get(c)) s += c;
        return in;
    }
    template <class T, std::enable_if_t<std::is_integral_v<T>, int> = 0>
    friend Scanner& operator>>(Scanner& in, T& x) {
        char c, f = '+';
        for (in.get(c); !isdigit(c); in.get(c))
            if constexpr (std::is_signed_v<T>) f = c;
        for (x = 0; isdigit(c); in.get(c)) x = x * 10 + c - '0';
        if constexpr (std::is_signed_v<T>) x = f == '-' ? -x : x;
        return in;
    }
    template <class T, std::enable_if_t<std::is_floating_point_v<T>, int> = 0>
    friend Scanner& operator>>(Scanner& in, T& x) {
        std::string s;
        in >> s;
        x = std::stod(s);
        return in;
    }
    template <class T, class U>
    friend Scanner& operator>>(Scanner& in, std::pair<T, U>& a) {
        return in >> a.first >> a.second;
    }
    template <class T>
    friend Scanner& operator>>(Scanner& in, std::vector<T>& a) {
        for (int i = 0, n = a.size(); i < n; i++) in >> a[i];
        return in;
    }
};

template <size_t S>
struct Printer {
    char buf[S], *l = buf, *r = buf + S - 1;
    int format = 0, precision = 15;
    ~Printer() { flush(); }
    void flush() {
        fwrite(buf, 1, l - buf, stdout);
        l = buf;
    }
    void put(const char& c) {
        *l++ = c;
        if (l == r) flush();
    }
    friend Printer& operator<<(Printer& out, const char& c) { return out.put(c), out; }
    friend Printer& operator<<(Printer& out, const char* s) {
        for (int i = 0; s[i] != '\0'; i++) out.put(s[i]);
        return out;
    }
    friend Printer& operator<<(Printer& out, const std::string& s) {
        for (int i = 0, n = s.size(); i < n; i++) out.put(s[i]);
        return out;
    }
    template <class T, std::enable_if_t<std::is_integral_v<T>, int> = 0>
    friend Printer& operator<<(Printer& out, T x) {
        static char s[40];
        static int i = 0;
        if (x == 0) {
            out.put('0');
            return out;
        }
        if constexpr (std::is_signed_v<T>) x = x < 0 ? out.put('-'), -x : x;
        while (x > 0) s[++i] = x % 10 + '0', x /= 10;
        while (i > 0) out.put(s[i--]);
        return out;
    }
    template <class T, std::enable_if_t<std::is_floating_point_v<T>, int> = 0>
    friend Printer& operator<<(Printer& out, T x) {
        std::ostringstream oss;
        oss << std::fixed << std::setprecision(out.precision) << x;
        return out << oss.str();
    }
    template <class T, class U>
    friend Printer& operator<<(Printer& out, const std::pair<T, U>& a) {
        return out << a.first << " \n"[out.format > 1] << a.second;
    }
    template <class T>
    friend Printer& operator<<(Printer& out, const std::vector<T>& a) {
        int n = a.size();
        for (int i = 0; i < n - 1; i++) out << a[i] << " \n"[out.format > 0];
        return n > 0 ? (out << a[n - 1]) : out;
    }
};

Scanner<1048576> in;
Printer<1048576> out;
```

具体使用

```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int n;
    in >> n;
    i64 ans = 0;
    for (int i = 0; i < n; i++) {
        int x;
        in >> x;
        ans += x;
    }
    out << ans << '\n';

    return 0;
}
```



## 封装快读快写

卡c++的cin和cout的读写速度时可以尝试

```c++
namespace IO {

char *p1, *p2, buf[1 << 20];
#ifdef missliker
#else
#define getchar() (p1 == p2 and (p2 = (p1 = buf) + fread(buf, 1, 100000, stdin), p1 == p2) ? EOF : *p1++)
#endif

template <typename T>
T read() {
    T x = 0;
    int f = 1;
    char ch = getchar();
    for (; ch < '0' or ch > '9'; ch = getchar()) {
        if (ch == '-') {
            f = -1;
        }
    }
    for (; ch >= '0' and ch <= '9'; ch = getchar()) {
        x = ((x << 3) + (x << 1)) + (ch - '0');
    }
    return x * f;
}

template <typename T>
void print(T x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}

} // namespace IO
```

```c++
int a = read<int>();
print(a);
```



## 普通

```c++
__int128 read() {
    __int128 x = 0;
    int f = 1;
    char ch = getchar();
    while (ch < '0' or ch > '9') {
        if (ch == '-') f = -1;
        ch = getchar();
    }
    while (ch >= '0' and ch <= '9') {
        x = ((x << 3) + (x << 1)) + (ch - '0');
        ch = getchar();
    }
    return x * f;
}

void print(__int128 x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
```
