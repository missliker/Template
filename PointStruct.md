### struct构造函数

```cpp
struct Point {
    int x, y;
    Point() { x = 0, y = 0; }
    Point(int neo_x, int neo_y) : x(neo_x), y(neo_y) {}
    Point operator+(const Point& t) { return {x + t.x, y + t.y}; }
    Point operator-(const Point& t) { return {x - t.x, y - t.y}; }
    bool operator==(const Point& t) const { return (x == t.x and y == t.y); }
    bool operator<(const Point& t) const { return (x == t.x ? y < t.y : x < t.x); }
};
vector<Point> dxy{{0, 1}, {0, -1}, {-1, 0}, {1, 0}, {1, 1}, {1, -1}, {-1, 1}, {-1, -1}};
```

```c++
// Point a;
// Point b(x, y);
```

