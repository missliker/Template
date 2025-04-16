# debug.h

```c++
#pragma once
#include <bits/stdc++.h>
#include <generator.h>

// cout/cerr输出bool值为true、false
struct BoolAlphaSetter {
    BoolAlphaSetter() {
        std::cerr.setf(std::ios_base::boolalpha);
        std::cout.setf(std::ios_base::boolalpha);
    }
};
static BoolAlphaSetter boolAlphaSetter;

// cout/cerr输出浮点数位数保留20位
struct Setprecision20 {
    Setprecision20() {
        std::cerr << fixed << setprecision(20);
        std::cout << fixed << setprecision(20);
    }
};
static Setprecision20 setprecision20;

template <typename T, std::size_t size = std::tuple_size<T>::value>
std::string to_debug(T, std::string s = "")
    requires(not std::ranges::range<T>); // 第一个模板函数声明，要求T不是一个范围类型, std::ranges::range<T>是C++20引入的概念
std::string to_debug(auto x)
    requires requires(std::ostream& os) { os << x; } // 第二个模板函数声明，要求x支持输出流操作
{
    std::ostringstream oss;
    oss.setf(std::ios_base::boolalpha); // oss输出bool值为true、false
    oss << fixed << setprecision(20);   // oss输出浮点数位数保留20位
    oss << x;
    return oss.str(); // 将流中的内容转换成字符串并返回
}
// It is OK.

// template <typename T, std::size_t size = std::tuple_size<T>::value>
// std::string to_debug(T, std::string s = "")
//     requires(not std::ranges::range<T>);
// std::string to_debug(auto x)
//     requires requires(std::ostream& os) { os << x; }
// {
//     return static_cast<std::ostringstream>(std::ostringstream() << x).str();
// }
// It is OK, but dont have boolalphasetter and setprecision20.

std::string to_debug(std::ranges::range auto x, std::string s = "") // x是一个范围类型
    requires(not std::is_same_v<decltype(x), std::string>)          // 要求x不是std::string类型
{
    for (auto xi : x) {
        s += ", " + to_debug(xi);
    }
    return "[" + s.substr(s.empty() ? 0 : 2) + "]"; // 返回一个字符串s，如果s空，直接返回[]，否则调用s.substr(2)，去掉开头的", "
}

template <typename T, std::size_t size> // T是一个元组或类似元组的类型（支持 std::get<I>(x) 获取元素），size是元素的个数
std::string to_debug(T x, std::string s)
    requires(not std::ranges::range<T>) // 要求x不是一个范围类型，但是可以是std::string类型
{
    [&]<std::size_t... I>(std::index_sequence<I...>) { // 接受生成的序列
        ((s += ", " + to_debug(std::get<I>(x))), ...); // get<I>(x)是类似元组的取法
    }(std::make_index_sequence<size>()); // 生成一个序列index_sequence<0, 1, 2, ..., size - 1>，传入lambda
    return "(" + s.substr(s.empty() ? 0 : 2) + ")"; // 返回一个字符串s，如果s空，直接返回()，否则调用s.substr(2)，去掉开头的", "
}

// 在宏定义中，括号中的 ... 是一个可变参数宏的标志，用于表示可以接受不定数量的参数，参数间用逗号隔开
// __VA_ARGS__ 是一个特殊的标识符，用于替代宏调用中的可变参数部分
#define debug(...) std::cerr << __FILE__ ":" << __LINE__ << ": (" #__VA_ARGS__ ") = " << to_debug(std::tuple(__VA_ARGS__)) << '\n'
```

