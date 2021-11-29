1. 最好多使用花括号包起来的初始化列表。

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