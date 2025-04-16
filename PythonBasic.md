# Python常用语法

## 读入与定义

-   读入多个变量并转换类型：`X, Y = map(int, input().split())`
-   读入列表：`X = eval(input())`
-   多维数组定义：`X = [[0 for j in range(0, 100)] for i in range(0, 200)]`

## 格式化输出

-   保留小数输出：`print("{:.12f}".format(X))` 指保留 12 位小数
-   对齐与宽度：`print("{:<12f}".format(X))` 指左对齐，保留 12 个宽度

## 排序

-   倒序排序：使用 `reverse` 实现倒序 `X.sort(reverse=True)`

-   自定义排序：下方代码实现了先按第一关键字降序、再按第二关键字升序排序。

    ```python
    X.sort(key=lambda x: x[1])
    X.sort(key=lambda x: x[0], reverse=True)
    ```

## 文件IO

-   打开要读取的文件：`r = open('X.txt', 'r', encoding='utf-8')`
-   打开要写入的文件：`w = open('Y.txt', 'w', encoding='utf-8')`
-   按行写入：`w.write(XX)`

## 增加输出流长度、递归深度

```python
import sys
sys.set_int_max_str_digits(200000)
sys.setrecursionlimit(100000)
```

## 自定义结构体

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

## 数据结构

-   模拟于 C++map ，定义：`dic = dict()`
-   模拟栈与队列：使用常见的 list 即可完成，`list.insert(0, X)` 实现头部插入、`list.pop()` 实现尾部弹出、`list.pop(0)` 实现头部弹出

## 其他

-   获取ASCII码：`ord()` 函数
-   转换为ASCII字符：`chr()` 函数