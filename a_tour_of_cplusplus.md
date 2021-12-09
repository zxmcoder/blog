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
    // static_assert(speed < C, "speed too fast!");
}

int main()
{
    // static_assert 和 constexpr一般配合使用
    f(11.0);
    // static_assert最重要的用途是为泛型编程中作为形参的类型设置断言

    // 其实对于static_assert 和 constexpr 都记住编译期间就行了。
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

#### 虚函数表和虚表指针

每个对象都有自己的虚表指针，不同的编译器虚表指针排布的位置可能不一样，一般都放在对象栈空间的最前面的位置，这根虚表指针指向每个继承了虚类的类中的虚函数表。

#### 关于虚析构函数

对于抽象类来说，因为其派生类的对象通常是通过抽象基类的接口操作的，所以基类中必须要有一个虚析构函数。

当我们使用一个基类指针释放派生类对象的时候，虚函数的调用机制能确保我们调用了正常的析构函数，然后该析构函数再隐式的调用其基类的构造函数和成员的析构函数。

#### 使用Override来显式的覆盖，避免出错。

#### 可以使用dynamic_cast运算符询问对应的基类引用能否转换成派生类

#### 函数返回一个指向自由存储上的对象的指针是非常危险的

一种解决办法是使用unique_ptr

函数返回`unique_ptr<Shape>`

使用`Vector<unique_ptr<Shape>>`

#### 当设计一个类的时候，必须仔细考虑对象是否会被拷贝以及如何拷贝的问题

#### 思考一下this的作用

类的函数存储在静态的代码区域，只有一份。

函数如何知道调用自己的是哪个对象呢？this告诉了这个函数调用自己的是谁。

#### 关于移动

最不合理的地方是operator+()中的res在拷贝后就不再使用了。事实上我们并不真的想要一个副本，我们只想把计算结果从函数中取出来。相比copy一个Vector对象，我们更希
移动res。

#### 右值引用

右值引用的含义就是引用了一个别人无法赋值的内容，所以我们可以安全的窃取它的值。

std::move()不会真的移动什么，而是负责将一个左值转换成右值。

#### =default运算符

类会自动的生成拷贝和移动操作，也可以自己=default指定使用某些初始化函数，当使用=default之后，其余的初始化函数将不会被生成。

当类中有指针或者引用的时候，最好显示的指定拷贝操作和移动操作。

#### explict声明

单个参数的构造函数可以被作为参数类型到类类型的转换。

但是有的时候我们不希望这种情况出现，这个时候在构造函数前面使用explict关键字就可以了。

> 因此除非你有充分的理由，否则最好把接受单个参数的构造函数声明成explict的。

#### 资源管理与资源句柄

绝大多数的情况下，使用vector, thread, unique_ptr……这些资源句柄要比直接使用指针好的多。

在C++中，最推荐的资源管理的方式是：栈+智能指针。

在C++中，RAII无处不在。

#### =delete抑制操作

#### 第四章总结

1. 具体类是最简单的类，与复杂类相比，应该优先的使用具体类。
2. 使用具体类来表达简单的概念或者是性能要求比较高的组件。
3. 有一些函数没必要定义为成员函数，例如operator+
4. 成员函数可以声明为const
5. 避免裸new和delete操作
6. 包含虚函数的类应该同时包含一个虚析构函数
7. override显式的声明函数覆盖
8. dynamic_cast 在 使用基类的引用或者指针的时候非常有用
9. 用传值的方式做函数返回，这个时候是移动而非拷贝。
10. 如果一个类写了自己的析构函数，那么极有可能需要自定义自己的移动和拷贝函数
11. 如果一个类被作为资源句柄，则需要为它提供构造函数，析构函数，以及非默认的拷贝和移动操作。
12. 默认情况下，把单参数的构造函数声明为explict的

#### 模板实例化发生的时机

发生在编译过程的后期，所以很多模板实例化的错误信息都很难看懂。

#### 模板的值参数

1. `using value_type = T`
2. `constexpt size_t size() { return N; }`

```
#include <iostream>

using namespace std;

template<typename T, size_t N>
struct Buffer
{
    using value_type = T;
    constexpr static size_t size() { return N; }
    T elem[N];
};

int main()
{
    cout << Buffer<char, 1024>::size() << endl;
}
```

#### using和typedef

```
#include <iostream>

using namespace std;

template<typename T, size_t N>
struct Buffer
{
    using value_type = T;
    constexpr static size_t size() { return N; }
    T elem[N];
};

int main()
{
    cout << Buffer<char, 1024>::size() << endl; 
    // 下面这两条typedef 或者是 using 的作用是一致的
    // 还可以关注一下typename的用法
    // using type = Buffer<char, 1024>::value_type;
    typedef Buffer<char, 1024>::value_type type;
    type c = 'c';
    cout << c << endl;
}
```

#### 第五章总结

1. 模板不存在分离式编译，用到模板的地方都应该#include相应的模板。
2. 使用模板的时候要保证它的定义在作用域内。
3. 把函数对象作为算法的参数。

#### 第六章总结

1. 不要重复造轮子
2. 有多种选择的时候，应该优先选用标准库而非其它库

#### string的短字符串优化的策略

长的字符串通过指针指向自由存储区，短的字符串直接保存在栈区域。

string的性能受程序运行时的环境特别大，当出现大量的短字符串的时候，如果没有短字符串优化的策略，会在堆区域产生内存碎片，而把字符串整体放在栈区域就不会有内存碎片的影响。

#### 多字符集处理

`using string = basic_string<char>`
`using Jstrlng = basic_string<Jchar>`

#### 正则表达式

对正则表达式不是很熟悉，现在是个学习的好机会。

#### 第七章总结

1. 返回string应该直接返回，利用它的移动语义。
2. 对于string而言，需要边界检测at()，考虑性能[]
3. regex应该在使用中学习
4. Raw"" 原始字符串很好用
5. 使用正则表达式要懂得克制，并且检查自己所写的正则。


#### 第八章总结

1. 使用流状态 fail 处理可恢复的I/O错误。
2. 不要试图拷贝一个文件流。
3. 记住iostream中没有const存在。
4. stringstream
5. fstream
6. 记得检验文件流是否正确打开了，文件流是否正确的绑定到文件上面。

#### 第九章总结

1. 标准容器是一个资源句柄。
2. 使用vector作为标准容器，除非有其它非常明确的理由。
3. vector的reverse不一定会带来性能收益。
4. vector删除erase操作的迭代器失效问题，调整vector大小之后都要注意这个问题。
5. at操作和[]
6. 对通常为空的序列，使用 forward list，单向列表在这方面做了优化。
7. 传递容器参数时，应传递引用，返回容器时，应返回值。
8. 通过实验来检查你设计的哈希函数是否令人满意。
9. 可以设计自己的hash策略。通过组合标准哈希函数和异或运算设计出的哈希函数通常有较好效果

#### 第十章总结

1. 对于模板来说，typename是必须的，它通知编译器，接下来的东西并不是一个value而是一种type，例如`typename C::iterator`，当然我们经常使用`using Iterator = typename C::iterator`
2. 通常不直接使用ostream_iterator和istream_iterator，而是将它们当做参数传递给算法。
3. 标准库算法中不包含push和erase这种操作，也就是说size不会改变。
4. 谓词不能改变参数，谓词机制让标准库的算法更加强大。

#### 关于类型函数的概念

1. 类型函数(type function)是指在编译期求值的函数，它接受一个类型作为实参或者返回一个类型作为结果。
2. 标准库对于像标签分发这种迭代器技术的支持是以简单类模板iterator_tratis的
形式提供的，iterator_tratis定义在`<iterator>`中
3. 标准库的类型谓词是一种简单的类型函数，它负责回答和本类型有关的基本问题。在头文件`<type_traits>`中有很多这样的类型谓词，在我们编写模板的时候非常有用。

#### 第十一章总结

1. C++库的概念是大而全不如小而精，所以C++中大部分时候只有库，而Java中却是大而全的框架。
2. RAII 和 资源句柄。
3. 除非迫不得已，否则连智能指针也不要用，资源句柄vector,thread是更好的选择。
4. 在需要 constexpr 大小的序列的地方使用 array。
5. make_share, make_unique, make_pair, make_tuple。
6. chrono时间计数 和 duration_cast转换成一个合适的时间单位。
7. 通常情况下， lambda 可以起到 bind()和 mem_fn()相似的作用。
8. 用bind()创建函数和函数对象的变形，用mem_fn()将类中的函数转换成普通的C函数。
9. 用function存储函数对象，可以被调用的函数的对象，可以被当作参数。
10. 编写代码的时候，尤其是编译期间的编程，type非常重要。

#### 第十二章总结

#### 第十三章总结 & C++并发一览

1. 不要把并发当成灵丹妙药，如果串行能解决问题，很明显串行更快。
2. 两个没有同步机制的线程同时使用cout会有问题。因为cout本身不是一个线程安全的对象。
3. defer_lock可以一次性获得所有的锁，但是通过锁的方式来通信和同步抽象层次太低了……
4. 一个利用条件变量和锁写的简单的生产者消费者模型。
5. future promise async() packaged_task()
6. packged_task()是一种资源句柄，它拥有一个promise且间接的负责其任务所拥有的资源。
7. 其实最好用的还是async(),无需关心底层的细节，async()真的很好用啊，异步。
8. 只要性能可以接受，就应该选择更高层次的抽象。
9. lock free 的设计太过复杂，留给专家吧！
10. 不要裸使用thread，从任务角度使用并发。
11. 读而不写，就不会有数据竞争的问题！
12. 最好的并发就是没有并发，最好的多线程就是看上去没有线程。
13. 仅调用一次：`static std::once_flag flag` `call_once()`
14. 线程局部存储 `thread_local`
15. 原子变量。mutex的成本太高，应该使用atomic，不是所有的类型都可以原子化的，store，load，fetch_add，fetch_sub，compare_exchange_weak，compare_exchange_strong，CAS，TAS，atomic_flag。
16. TAS用来保证绝对无锁，无锁数据结构(趋近于并行的过程)，boost.lock_free。
17. std::this_thread
18. async()不显式的获取它的future，它就会变成同步阻塞的类型。
19. thread.detach()在不关心返回值的时候特别好用，例如(点赞+1)写数据库一类的操作，虽然是写但是不是很重要。
20. volatile只是表示变量易变，与线程没有任何关系。C++中不加锁应该用atomic变量。


