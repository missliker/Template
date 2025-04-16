# 前置知识，位运算
```cpp
__builtin_popcount(x) 	// 正整数int x 的二进制里1的个数
__builtin_popcountll(x)	// x为long long的时候用这个
__builtin_parity(x)   // x的二进制里面的奇偶性
__builtin_parityll(x) 
__builtin_ctz(x)	//x的二进制末尾 0 的个数
__builtin_clz(x)	//x的二进制开头 0 的个数
__lg(x) 	//log2向下取整，只接受整数，返回整数,可以用31 - __builtin_ctz(x)平替
log2(x)		//接受浮点数，返回浮点数
以上均为O(1)复杂度

bitset<1280> a，//可以理解为一个长度为1280的bool数组，即bool a[1280]
//但是bool数组一个元素会占用一个字节
a.any()		//判断a里面是否存在1
a.none()	//判断a里面是否全为0，与a.any()是相反的
a.count()	//统计a里面有多少个1
a.set(x)	//把x这一位变成1
a.set()		//把所有都变成1
a.reset(x)	//把x这一位变成0
a.reset()	//把所有都变成0
a.size()	//类似vetcor数组的size，这里是1280
a._Find_first()	//a中第一个等于1的位置
a._Find_next(x)	//第x位的下一个等于1的位置
for(int i = a._Find_first();i != a.size();i = a._Find_next(i))
	cout << i << ' ';//遍历a中所有等于1的位置
//时间复杂度O(n/w + 1的位数)
auto pa = (ull*)&a	//可以把bitset按字节砍断，0~63为pa[0],64~127为pa[1],不常用
a.to_string()	//可以把a转成string去看整个a是什么，主要是调试有用
```

# 常用技巧
```c++
#define set_bit(x, n)    ((x) |= (1LL << (n)))                    // 将x的的第n位设置为1
#define clear_bit(x, n)  ((x) &= ~(1LL << (n)))                   // 将x的第n位清零
#define toggle_bit(x, n) ((x) ^= (1LL << (n)))                    // 将x的第n位取反
#define test_bit(x, n)   ((x) & (1 << (n)))                       // 输出x的第n位
#define lowbit(x)        ((x) -= (x & (-x)))                      // 舍弃最后一位1
#define __bit(x)         (bitset<32>(x))                          // 把十进制数x按二进制输出
#define is_power_of_2(x) (((x) > 0) and (((x) & ((x) - 1)) == 0)) // 判断是否是2的倍数
```

用法：
```c++
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;

#ifdef missliker
#include "debug.h"
#else
#define debug(...) void(0)
#endif

#define set_bit(x, n)    ((x) |= (1LL << (n)))                    // 将x的的第n位设置为1
#define clear_bit(x, n)  ((x) &= ~(1LL << (n)))                   // 将x的第n位清零
#define toggle_bit(x, n) ((x) ^= (1LL << (n)))                    // 将x的第n位取反
#define test_bit(x, n)   ((x) & (1 << (n)))                       // 输出x的第n位
#define lowbit(x)        ((x) -= (x & (-x)))                      // 舍弃最后一位1
#define __bit(x)         (bitset<32>(x))                          // 把十进制数x按二进制输出
#define is_power_of_2(x) (((x) > 0) and (((x) & ((x) - 1)) == 0)) // 判断是否是2的倍数

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    int x = 0;
    set_bit(x, 0);
    set_bit(x, 2);
    set_bit(x, 3);
    set_bit(x, 5);
    debug(x);
    debug(__bit(x));
    lowbit(x);
    debug(__bit(x));
    debug(x);
    lowbit(x);
    debug(__bit(x));
    debug(x);

    debug(is_power_of_2(3));

    return 0;
}
```