# 快读快写

## jly版快读
```cpp
namespace iof {
const int Buffer_MAXSIZE = 0xfffff;
const char endl = '\n';

class ifast {
   public:
    ifast& operator>>(char& ch);
    ifast& operator>>(char* s);
    ifast& operator>>(std::string& string);
    ifast& operator>>(bool& x) { return read_unsigned_integer(x); }
    ifast& operator>>(int& x) { return read_signed_integer(x); }
    ifast& operator>>(long long& x) { return read_signed_integer(x); }
    ifast& operator>>(unsigned int& x) { return read_unsigned_integer(x); }
    ifast& operator>>(unsigned long long& x) { return read_unsigned_integer(x); }

    void getline(std::string& string);
    void getline(char* s);

    auto hasMoreToken() { return front <= back; }

   private:
    template <typename T>
    ifast& read_signed_integer(T& x);

    template <typename T>
    ifast& read_unsigned_integer(T& x);

    char getchar();
    char buffer[Buffer_MAXSIZE]{};
    char* front = buffer;
    char* back = buffer;
    char next_char{};
} fin;

class ofast {
   public:
    ofast& operator<<(const char& c);
    ofast& operator<<(const char* c);
    ofast& operator<<(const std::string& s);
    ofast& operator<<(const bool& x) { return print_integer(x); }
    ofast& operator<<(const int& x) { return print_integer(x); }
    ofast& operator<<(const long long& x) { return print_integer(x); }
    ofast& operator<<(const unsigned int& x) { return print_integer(x); }
    ofast& operator<<(const unsigned long long& x) { return print_integer(x); }

    void flush();
    ~ofast() { flush(); }

   private:
    void putchar(const char& c) { *top++ = c; }
    char buffer[Buffer_MAXSIZE]{};
    char* top = buffer;

    template <typename T>
    ofast& print_positive_integer(const T& x);

    template <typename T>
    ofast& print_integer(const T& x);
} fout;

char ifast::getchar() {
    if (front == back) {
        back = buffer + fread(buffer, 1, Buffer_MAXSIZE, stdin);
        front = buffer;
    }
    return *(front++);
}

void ifast::getline(std::string& string) {
    string.clear();
    for (*this >> next_char; hasMoreToken() && next_char != '\n'; next_char = getchar()) {
        string.push_back(next_char);
    }
}

void ifast::getline(char* s) {
    for (char* p = s; hasMoreToken() && next_char != '\n'; next_char = getchar(), p++) {
        *p = next_char;
    }
}

ifast& ifast::operator>>(char& ch) {
    do {
        ch = getchar();
    } while (hasMoreToken() && (ch == ' ' || ch == '\n'));
    return *this;
}

ifast& ifast::operator>>(char* s) {
    *this >> next_char;
    for (char* p = s; hasMoreToken() && next_char != ' ' && next_char != '\n'; next_char = getchar(), p++) {
        *p = next_char;
    }
    return *this;
}

ifast& ifast::operator>>(std::string& string) {
    *this >> next_char;
    string.clear();
    for (; hasMoreToken() && next_char != ' ' && next_char != '\n'; next_char = getchar()) {
        string.push_back(next_char);
    }
    return *this;
}

template <typename T>
ifast& ifast::read_unsigned_integer(T& x) {
    *this >> next_char;
    x = 0;
    do {
        x = (x << 1) + (x << 3) + next_char - '0';
        next_char = getchar();
    } while (hasMoreToken() && next_char != ' ' && next_char != '\n');
    return *this;
}

template <typename T>
ifast& ifast::read_signed_integer(T& x) {
    *this >> next_char;
    auto isNegative = next_char == '-';
    if (isNegative) {
        next_char = getchar();
    }
    x = 0;
    do {
        x = (x << 1) + (x << 3) + next_char - '0';
        next_char = getchar();
    } while (hasMoreToken() && next_char != ' ' && next_char != '\n');
    if (isNegative) {
        x = -x;
    }
    return *this;
}

ofast& ofast::operator<<(const char& c) {
    putchar(c);
    return *this;
}

ofast& ofast::operator<<(const char* c) {
    for (auto* p = c; *p != '\0'; p++) {
        putchar(*p);
    }
    return *this;
}

ofast& ofast::operator<<(const std::string& s) {
    for (const auto& i : s) {
        putchar(i);
    }
    return *this;
}

void ofast::flush() {
    fwrite(buffer, 1, top - buffer, stdout);
    top = buffer;
}

template <typename T>
ofast& ofast::print_integer(const T& x) {
    if (x > 0) {
        return print_positive_integer(x);
    } else if (x == 0) {
        putchar('0');
        return *this;
    } else {
        putchar('-');
        return print_positive_integer(-x);
    }
}

template <typename T>
ofast& ofast::print_positive_integer(const T& x) {
    if (x > 9) {
        print_positive_integer(x / 10);
    }
    putchar(x % 10 + '0');
    return *this;
}

}  // namespace iof

iof::ifast fin;
iof::ofast fout;
```

## 封装超快读(更短更常用更快)

```c++
template <std::size_t S>
struct Scanner {
    char buf[S], *l = buf, *r = buf;
    bool flush() {
        l = buf;
        r = l + fread(buf, 1, S, stdin);
        return l == r;
    }
    void get(char& c) { c = l == r and flush() ? ' ' : *l++; }
    friend Scanner& operator>>(Scanner& in, char& c) { return in.get(c), in; }
    friend Scanner& operator>>(Scanner& in, char* s) {
        for (in.get(s[0]); isspace(s[0]); in.get(s[0]));
        for (int i = 0; !isspace(s[i]) or (s[i] = '\0'); i++) in.get(s[i + 1]);
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

template <std::size_t S>
struct Printer {
    char buf[S], *l = buf, *r = buf + S - 1;
    int format = 0, precision = 20;
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

Scanner<1 << 20> fin;
Printer<1 << 20> fout;
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

```cpp
template <typename T>

concept INT248 = sizeof(T) >= 2 and is_integral_v<T>;

namespace io {

const int sz = 1 << 12;

char buf[sz], *p1, *p2;

char gc() { return p1 == p2 ? (p2 = (p1 = buf) + fread(buf, 1, sz, stdin), (p1 == p2 ? ' ' : *p1++)) : *p1++; }

void rd(INT248 auto &x) {

    int f = 1;

    char c = gc();

    for (; c < '0' or c > '9'; c = gc()) {

        if (c == '-') f = -1;

    }

    for (x = 0; c <= '9' and c >= '0'; c = gc()) {

        x = x * 10 + (c & 15);

    }

    x *= f;

}

void rd(char &c) { while (isspace(c = gc())); }

void rd(string &s) {

    char c;

    while (isspace(c = gc()));

    for (; not isspace(c); c = gc()) s += c;

}

};  // namespace io

template <typename Tuple, typename F, size_t... N>

void TupleCall(Tuple &t, F &&f, index_sequence<N...>) {

    (f(get<N>(t)), ...);

}

template <typename... Args>

ostream &operator<<(ostream &out, const tuple<Args...> &t) {

    TupleCall(t, [&](auto &&a) { out << a << ' '; }, make_index_sequence<sizeof...(Args)>{});

    return out;

}

template <typename T1, typename T2>

ostream &operator<<(ostream &out, const pair<T1, T2> &t) {

    return out << t.first << ' ' << t.second;

}

template <typename T>

concept hasBegin = requires(T a) { a.begin(); };

template <typename T>

    requires hasBegin<T> && (!is_same_v<string, T>)

ostream &operator<<(ostream &out, const T &v) {

    for (auto &&x : v) out << x << ' ';

    return out;

}

template <typename T, typename... Args>

void print(T &&t, const Args &...args) {

    cout << t;

    if constexpr (sizeof...(args)) {

        cout << ' ';

        print(args...);

    } else

        cout << endl;

}

template <typename... Args>

void tprd(tuple<Args...> &t) {

    TupleCall(t, [&](auto &&a) { io::rd(a); }, make_index_sequence<sizeof...(Args)>{});

}

template <typename T = int>

auto rd() {

    T res;

    io::rd(res);

    return res;

}

template <typename T1, typename T2, typename... Args>

auto rd() {

    tuple<T1, T2, Args...> res;

    tprd(res);

    return res;

}

template <typename T = int>

auto rd(size_t n, size_t off = 0) {

    vector<T> v(off + n);

    for (auto i : range(off, off + n)) io::rd(v[i]);

    return v;

}

template <typename T1, typename T2, typename... Args>

auto rd(size_t n, size_t off = 0) {

    vector<tuple<T1, T2, Args...>> v(off + n);

    for (auto i : range(off, off + n)) tprd(v[i]);

    return v;

}
```