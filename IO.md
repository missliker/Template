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

