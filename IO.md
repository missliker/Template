```c++
namespace IO {

char *p1, *p2, buf[1 << 20];
#ifdef Iris
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



# 普通

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



# 更新

```c++
struct IO_Tp {
    const static int _I_Buffer_Size = 10 << 20;
    char _I_Buffer[_I_Buffer_Size], *_I_pos = _I_Buffer;

    const static int _O_Buffer_Size = 5 << 20;
    char _O_Buffer[_O_Buffer_Size], *_O_pos = _O_Buffer;

    char m[10000][4];
    uint32_t n[0x10000];

    uint16_t pack2(char* p) {
        uint16_t res;
        std::copy(p, p + 2, reinterpret_cast<char*>(&res));
        return res;
    }

    IO_Tp() {
        {
            int x = 0;
            for (char i = '0'; i <= '9'; ++i)
                for (char j = '0'; j <= '9'; ++j)
                    for (char k = '0'; k <= '9'; ++k)
                        for (char l = '0'; l <= '9'; ++l) m[x][0] = i, m[x][1] = j, m[x][2] = k, m[x][3] = l, ++x;
        }
        {
            for (int i = 0; i != 0x10000; ++i) n[i] = -1;
            char s[2];
            int x = 0;
            for (s[0] = '0'; s[0] <= '9'; ++s[0])
                for (s[1] = '0'; s[1] <= '9'; ++s[1]) n[pack2(s)] = x++;
        }

        fread(_I_Buffer, 1, _I_Buffer_Size, stdin);
    }
    ~IO_Tp() { fwrite(_O_Buffer, 1, _O_pos - _O_Buffer, stdout); }

    IO_Tp& operator>>(int& x) {
        while (!isdigit(*_I_pos)) ++_I_pos;
        x = *_I_pos++ - '0';
        while (~n[pack2(_I_pos)]) x = x * 100 + n[pack2(_I_pos)], _I_pos += 2;
        if (isdigit(*_I_pos)) x = x * 10 + (*_I_pos++ - '0');
        return *this;
    }

    IO_Tp& operator<<(char ch) {
        *_O_pos++ = ch;
        return *this;
    }

    void print4(uint32_t x) { _O_pos = std::copy(m[x], m[x] + 4, _O_pos); }

    IO_Tp& operator<<(uint32_t x) {
        switch (x) {
        default: print4(x / 1000000), x %= 1000000; [[fallthrough]];
        case 100000 ... 999999: print4(x / 100), x %= 100; [[fallthrough]];
        case 10 ... 99: print4(x * 100), _O_pos -= 2; break;
        case 100000000 ... 999999999: print4(x / 100000), x %= 100000; [[fallthrough]];
        case 10000 ... 99999: print4(x / 10), x %= 10; [[fallthrough]];
        case 0 ... 9: *_O_pos++ = '0' + x; break;
        case 10000000 ... 99999999: print4(x / 10000), x %= 10000; [[fallthrough]];
        case 1000 ... 9999: print4(x); break;
        case 1000000 ... 9999999: print4(x / 1000), x %= 1000; [[fallthrough]];
        case 100 ... 999: print4(x * 10), _O_pos -= 1; break;
        }
        return *this;
    }

    IO_Tp& operator<<(int x) {
        if (x >= 0) *this << uint32_t(x);
        else *this << '-' << uint32_t(-x);
        return *this;
    }
} IO;
```

