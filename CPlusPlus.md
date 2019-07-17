## C++

在阅读ClickHouse 时， 里面有使用到很多`c++17` 引入的语法，有一些可能是C++11/C++14 但平时用的少，一起在下面学习
###STL  push_back   vs  emplace
emplace_back  与C++ 引入的 std::forward 语义有关系 
不需要构建临时对象，节省资源开销

###委托构造
构造函数:  其他构造函数() {} 

###Lambda 表达式
```c++
[ caputrue ] ( params ) opt -> ret { body; };
```

