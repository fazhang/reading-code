## 跟着开源项目学技术

ClickHouse 源代码学习

Date:  2019/7/10   
https://github.com/yandex/ClickHouse.git

### 1、`${BASH_SOURCE}` 
https://stackoverflow.com/questions/35006457/choosing-between-0-and-bash-source
脚本中用于标识当前文件的一个变量，适用于Bash，  而$0 是shell 的变量，两者略有区别

###2、`$CURDIR` 
取文件 自身路径
通过dirname 取文件相对路径，然后cd 到文件路径， 再执行pwd 就取到了文件的绝对路径

>启发：如果写Makefile 要获取绝对路径时，也可以类似操作
```bash
CURDIR=$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)
```

###3、shell 变量赋值
```bash
PBUILDER_AUTOUPDATE=${PBUILDER_AUTOUPDATE=4320}
```
此处语法见
https://www.cnblogs.com/fhefh/archive/2011/04/22/2024750.html
:=句法、=句法、:-句法、-句法、=?句法、?句法、:+句法、+句法
:=  与=  的区别是原变量值是否为空值

###4、 `[  ]`  与 `[[ ]]`  的区别 
在[ ] 中不能使用逻辑操作符， 而后者可以。
https://www.cnblogs.com/zeweiwu/p/5485711.html
`[[  ]]`  是shell   关键字   `[ ]` 是builtin 内置命令
https://www.computerhope.com/unix/bash/index.htm

`[[` 内部支持正则匹配。 可以写如下语句 
```bash
while [[ $1 == --* ]]
do   
	#something
done
```
shell 教程 http://tldp.org/LDP/Bash-Beginners-Guide/html/index.html

http://cn.linux.vbird.org/linux_basic/0340bashshell-scripts.php#script



###5、`Uncrustify.cfg`
代码美化工具，配置运行后，可自动调整代码格式,  和clang-format 类似，还有一种astyle  
https://github.com/uncrustify/uncrustify

###6、`.clang-format`
代码格式化使用  
[http://releases.llvm.org/download.html#8.0.0](http://releases.llvm.org/download.html#8.0.0)
llvm 项目提供了编译好的二进制文件，下载即可用

###7、`pushd` `popd` 
shell builtin 的两个命令， 用于多目录切换，类似于栈的结构来保存多个目录信息

###8、`include-what-you-use`
https://github.com/include-what-you-use/include-what-you-use
IWYU  是clang中，一个分析头文件的工具， 分析符号引用 。
目的是去掉不必要的头文件(go语言里面有类似的能力)，当然会有一些错误的信息
在Cmake文件中有如下代码，是一样的用途
```makefile
if (USE_INCLUDE_WHAT_YOU_USE)
    set (CMAKE_CXX_INCLUDE_WHAT_YOU_USE ${IWYU_PATH})
endif ()
```

###9、SIMD c++ 库  vector class library 
https://www.agner.org/optimize/vectorclass.pdf
https://www.agner.org/optimize/#vectorclass

###10、catboost 
yandex 的开源机器学习算法库

###11、bash  trap 
https://www.ibm.com/developerworks/cn/aix/library/au-usingtraps/index.html

###12、 shell   `set -e `
http://www.ruanyifeng.com/blog/2017/11/bash-set.html

###13、`__has_include`
c++17 引入的源文件包含指令
https://zh.cppreference.com/w/cpp/preprocessor/include