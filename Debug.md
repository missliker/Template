# debug.h

```c++
#include <bits/stdc++.h>
// using namespace std;

struct BoolAlphaSetter {
    BoolAlphaSetter() {
        std::cerr.setf(std::ios_base::boolalpha);
        std::cout.setf(std::ios_base::boolalpha);
    }
};
static BoolAlphaSetter boolAlphaSetter;

template <class T, std::size_t size = std::tuple_size<T>::value>
std::string to_debug(T, std::string s = "")
    requires(not std::ranges::range<T>);
std::string to_debug(auto x)
    requires requires(std::ostream& os) { os << x; }
{
    std::ostringstream oss;
    oss.setf(std::ios_base::boolalpha);
    oss << x;
    return oss.str();
}

// std::string to_debug(auto x)
//     requires requires(std::ostream& os) { os << x; }
// {
//     return static_cast<std::ostringstream>(std::ostringstream() << x).str();
// }

std::string to_debug(std::ranges::range auto x, std::string s = "")
    requires(not std::is_same_v<decltype(x), std::string>)
{
    for (auto xi : x) {
        s += ", " + to_debug(xi);
    }
    return "[" + s.substr(s.empty() ? 0 : 2) + "]";
}
template <class T, std::size_t size>
std::string to_debug(T x, std::string s)
    requires(not std::ranges::range<T>)
{
    [&]<std::size_t... I>(std::index_sequence<I...>) {
        ((s += ", " + to_debug(std::get<I>(x))), ...);
    }(std::make_index_sequence<size>());
    return "(" + s.substr(s.empty() ? 0 : 2) + ")";
}

#define debug(...) std::cerr << __FILE__ ":" << __LINE__ << ": (" #__VA_ARGS__ ") = " << to_debug(std::tuple(__VA_ARGS__)) << "\n"
```

