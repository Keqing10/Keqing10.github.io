---
title: 三路比较运算符
category_bar: true
math: false
tags: [C++, 比较, 排序]
category: [C++]
date: 2025-12-16 15:49:02
---

C++20起，支持使用`<=>`重载来方便地生成其他各种比较运算符的重载，包括：`<, <=, >, >=, ==, !=`。
# 重载规则
关于`<=>`的重载有两种方式，一种是默认方式，会生成全部的比较符号重载；另一种是自定义重载，此时不会生成`==`和`!=`重载，需要自己再额外声明对`==`的重载（默认或自定义）。
使用如下类测试：

```cpp
class Stu {
public:
    std::string name;
    int id;
    Stu(const std::string& n, int i) : name(n), id(i) {}
    ~Stu() = default;
};
```

## 三路运算符默认重载
只增加成员函数：
```cpp
auto operator<=>(const Stu& other) const = default;
```
这时会生成**全部**的重载，默认按照成员变量顺序进行比较。字符串使用字典序。
不需要手动再增加对于`==`的重载，其实是编译器也隐式增加了：
```cpp
bool operator==(const Stu& other) const = default;
```
但如果显式声明了对`==`的重载，`==`和`!=`就会使用等于重载来计算。
如果声明了对`!=`的重载，则`==`不可用。
如果声明对`==`的为`delete`，则`==`和`!=`均不可用。
## 三路运算符自定义重载
当自定义重载时，只会默认生成`<, <=, >, >=`的重载，需要额外声明对于`==`的重载，可以是`default`也可以自定义。

| 重载声明 | 可用的运算符 |
| :------: | :----------: |
|   <=>    | <, <=, >, >= |
|    ==    |    ==, !=    |
|    !=    |      !=      |
## 例子
```cpp
// 重载定义
auto operator<=>(const Stu& other) const
{
    std::cout << "Custom <=> operator called" << std::endl;
    if (auto res = name <=> other.name; res != 0) return res;
    return id <=> other.id;
}
bool operator==(const Stu& other) const {
    std::cout << "Custom == operator called" << std::endl;
    return name == other.name && id == other.id;
}

// 测试代码
int main() {
    Stu a("a", 1), b("b", 2), c("c", 3);
    if (a < b) {
        std::cout << "a is less than b." << std::endl;
    } else {
        std::cout << "a is not less than b." << std::endl;
    }
    if (a == c) {
        std::cout << "a is equal to c." << std::endl;
    } else {
        std::cout << "a is not equal to c." << std::endl;
    }
    if (a != c) {
        std::cout << "a is not equal to c." << std::endl;
    } else {
        std::cout << "a is equal to c." << std::endl;
    }
    return 0;
}
```
输出如下：
```txt
Custom <=> operator called
a is less than b.
Custom == operator called
a is not equal to c.
Custom == operator called
a is not equal to c.
```
可以看到比较`a == c`和`a != c`都调用了`operator==`。是因为没有显示声明`operator!=`，所以`a != c`被隐式转换为`!(a == c)`。

# 比较类
有三种比较类别用于`operator<=>`：
1. `std::strong_ordering`：严格的全序，适用于内置整数或`std::string`等。等价即相等。
2. `std::weak_ordering`：全前序，可以把不同对象视为等价于排序（equivalent）但不一定相等。例如大小写不敏感的排序。
3. `std::partial_ordering`：部分顺序，有可能出现unordered的结果，适用于浮点数、复杂数学对象等。例如浮点数与NaN的比较。

要使用这些类，需要包含头文件`<compare>`。
使用示例：
```cpp
std::strong_ordering operator<=>(const Stu& o) const {
    if (auto r = name <=> o.name; r != 0) return r; // std::string::operator<=> -> strong
    return id <=> o.id;                             // int -> strong
}

// 字母大小写不敏感排序的例子
std::weak_ordering operator<=>(const Stu& o) const {
    std::string a = name, b = o.name;
    std::transform(a.begin(), a.end(), a.begin(), ::tolower);
    std::transform(b.begin(), b.end(), b.begin(), ::tolower);

    if (a < b) return std::weak_ordering::less;
    if (a > b) return std::weak_ordering::greater;

    // a == b (case-insensitive) -> treat id as tie-breaker but still weak_ordering
    if (id < o.id) return std::weak_ordering::less;
    if (id > o.id) return std::weak_ordering::greater;
    return std::weak_ordering::equivalent;
}

struct F {
    double v;
    std::partial_ordering operator<=>(const F& o) const {
        return v <=> o.v; // double 的 <=> 返回 partial_ordering
    }
};
```

## strong_ordering的比较结果
```cpp
// <compare>
using _Compare_t = signed char;
enum class _Compare_eq : _Compare_t { equal = 0, equivalent = equal };
enum class _Compare_ord : _Compare_t { less = -1, greater = 1 };
enum class _Compare_ncmp : _Compare_t { unordered = -128 };

struct strong_ordering {
    static const strong_ordering less;
    static const strong_ordering equal;
    static const strong_ordering equivalent;
    static const strong_ordering greater;
    
    _Compare_t _Value;
};

inline constexpr strong_ordering strong_ordering::less{static_cast<_Compare_t>(_Compare_ord::less)};
inline constexpr strong_ordering strong_ordering::equal{static_cast<_Compare_t>(_Compare_eq::equal)};
inline constexpr strong_ordering strong_ordering::equivalent{static_cast<_Compare_t>(_Compare_eq::equivalent)};
inline constexpr strong_ordering strong_ordering::greater{static_cast<_Compare_t>(_Compare_ord::greater)};
```

其中`equal`强调真正的相等，与`operator==`对应，而`equivalent`强调排序上的等价，但不一定相等，对于`strong_ordering`两者没有区别，通常用`equal`。

## weak_ordering的比较结果
```cpp
struct weak_ordering {
    static const weak_ordering less;
    static const weak_ordering equivalent;
    static const weak_ordering greater;

    _Compare_t _Value;
};

inline constexpr weak_ordering weak_ordering::less{static_cast<_Compare_t>(_Compare_ord::less)};
inline constexpr weak_ordering weak_ordering::equivalent{static_cast<_Compare_t>(_Compare_eq::equivalent)};
inline constexpr weak_ordering weak_ordering::greater{static_cast<_Compare_t>(_Compare_ord::greater)};
```

## partial_ordering的比较结果
```cpp
struct partial_ordering {
    static const partial_ordering less;
    static const partial_ordering equivalent;
    static const partial_ordering greater;
    static const partial_ordering unordered;

    _Compare_t _Value;
};

inline constexpr partial_ordering partial_ordering::less{static_cast<_Compare_t>(_Compare_ord::less)};
inline constexpr partial_ordering partial_ordering::equivalent{static_cast<_Compare_t>(_Compare_eq::equivalent)};
inline constexpr partial_ordering partial_ordering::greater{static_cast<_Compare_t>(_Compare_ord::greater)};
inline constexpr partial_ordering partial_ordering::unordered{static_cast<_Compare_t>(_Compare_ncmp::unordered)};
```
## 比较类的使用
1. 直接和0做比较：结果<0，左<右；结果>0，左>右。
2. 使用常量检查：`auto r = a <=> b; r == strong_ordering::less`。
3. 使用辅助函数：`is_eq(const partial_ordering _Comp)` `is_neq()` `is_lt()` `is_lteq()` `is_gt()` `is_gteq()`。

一般情况下重载`<=>`之后可以直接使用各种运算符比较原始类型。
