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

#### 第二章总结

1. enum并不是最优解，多使用enum class，在必要的情况下，可以使用操作符重载的方式使用enum class。
2. 避免使用裸union，把它置于类中形成类型域。

#### 关于terminate()函数

> C++中，异常不可以忽略，当异常找不到匹配的catch字句时，会调用系统的库函数terminate()（在头文件excpetion中），默认情况下，terminate（）函数调用标准C库函数abort（）使程序终止而退出。当调用abort函数时，程序不会调用正常的终止函数，也就是说，全局对象和静态对象的析构函数不会执行。

> 通过使用标准的set_terminate()函数，可以设置自己的terminate（)函数。自定义的terminate()函数不能有参数，而且返回值类型为void。另外，terminate函数不能返回也不能抛出异常，它必须终止程序。如果terminate函数被调用，这就意味着问题已经无法解决了。

#### 静态断言

```
#include <iostream>

using namespace std;

void f(const double speed)
{
    const auto local_max = 10;
    constexpr auto C = 9.0;

    static_assert(local_max < C, "too fast!");
    static_assert(speed < C, "speed too fast!");
}

int main()
{
    // static_assert 和 constexpr一般配合使用
    f(11);
    // static_assert最重要的用途是为泛型编程中作为形参的类型设置断言
    return 0;
}
```

#### 第三章总结

1. 可以把永远不会抛出异常的函数声明为noexcept，但是一旦真的抛出异常，程序会调用terminate直接终止。
2. 不变式的概念是设计类的关键，前置条件也在设计函数的过程中起着类似的作用。
3. 不变式的概念在C++中由构造函数和析构函数支撑的资源管理概念的基础。
4. 不要在头文件中声明非内联函数
5. 不要在头文件中使用using namespace
6. 让构造函数设计不变式，不满足就抛出异常。