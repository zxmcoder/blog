#### 最好多使用花括号包起来的初始化列表。

```
#include <iostream>

using namespace std;

int main()
{
    // narrowing conversion of ‘7.2000000000000002e+0’ from ‘double’ to ‘int’ [-Wnarrowing]
    int i{7.2};
    // 排除了一些可能出现的隐式类型的转换
    return 0;
}
```

#### const和constexpr的区别

```
#include <iostream>
#include <cmath>

using namespace std;

// 要想定义成 constexpr，函数必须非常简单。
// constexpr 函数可以接受非 常实参，但此时其结果不再是一个常量表达式。
constexpr double square(double x)
{
    return x * x;
}

int main()
{
    // const 我承诺我不改变这个值，主要用于说明接口。
    // constexpr 在编译时求值，主要用于说明常量，作用是把数据放在只读内存中以及提高性能。

    const int dmv = 17;
    int var = 17;

    // 这个表达式通过编译只有当square可以在编译时求值。
    // 所以需要用constexpr说明函数square
    constexpr double max1 = 1.4 * square(dmv); 

    // 这条语句无法通过编译
    // constexpr double max2 = 1.4 * square(var);

    // 这条语句可以通过编译
    const double max3 = 1.4 * square(var);

    // 其实只要记住constexpr是编译时求值就行了
    return 0;
}
```

#### 第一章总结

1. 请关注编程技术，而非语言特性。
2. 如果一个函数可能得在编译时求值，那么把它声明成constexpr;
3. 当指明了类型名字时 建议在声明语句中使用{}形式的初始值列表;
4. 当使用 auto 关键字时，建议在声明语句中使用=进行初始化; 
5. 如果你还不打算初始化一个变量，那就先别声明它。
6. 尽量避免窄化类型转换。