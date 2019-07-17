## Linux系统

### 操作系统
>以下为各种细碎的记录

Linux 承袭了UNIX `everything is a file `的设计哲学。在系统里面无论是真实的文件、外接设备、虚拟设备、网络socket、内核变量等，均体现为FILE. 
体现在代码中， 所有对象均可使用read/wirte 来做操作(对应是struct 里面的read/write  函数指针)。
内核暴露出来一些控制变量，也可在虚拟的/proc 文件系统中读取和更改。


## Bash 

### grep 
主要基于https://linux.die.net/man/1/grep  <br/>
`-V` `--version`<br/>
	这个没啥好说的<br/>
`-E`  `--extended-regexp`
	使用`扩展正则表达式ERE` ，这里与`-e ` 有关    
	`--` 开头的参数在Linux Shell 里面被称为长选项，一般会通过getopt getopt_long 函数来实现, `#include <getopt.h>`。 假定指定的匹配pattern以-开头。 shell 无法分辨传入的是选项，还是参数。 所以可以用长选项来实现
`-e` `--regexp`
	使用`-e` 时，仅能传递一个参数，当要传递多个时，可以grep -e ARG1  -e ARG2 -e ARG3 
`-f` `--file`  
	从文件中获取pattern
`-F` `--fixed-strings`
	不要识别为正则表达式， 以字面值来理解，不能和`-E` 一起用。
`-i` `-y` `--ignore-case`
	忽略大小写，都忽略了，所以输入的pattern 与文件行都忽略
`-v` `--invert-match`
	与普通的相反， 输出未匹配的内容
`-w` `--word-regexp`
	w=word，仅匹配整词。 vim 查找整词时，一般用`/\<word\>`
`-G` `--basic-regexp`
	基础正则表达式，默认
`-P` ` --perl-regexp`
	使用Perl 正则表达式语法
	试图查找TAB \t 时， 可以尝试 grep -P '\t' file 。 
	由于使用了perl 语法，其中有$时，也要\$
	也可以尝试 grep  $"\t" file  来匹配TAB 
`-x` `--line-regexp`
	整行匹配，文件头尾有空格时，是匹配不到的，可以考虑用sed 来做替换
`-c` `--count`
	输出匹配的个数，不输出普通的匹配文本
`--color=[WHEN]` `--colour=[WHEN]`
	never:不带颜色
	always: 任何情况下都输出颜色控制，通过管道处理时，可能会有问题。
	auto:	仅当输出终端时带颜色
	颜色配置与环境环境  GREP_COLOR  GREP_OPTIONS 相关
`-l` `--files-with-matches`
	匹配pattern 的文件名
`-L` `--files-without-match`
	与`-l` 相反， 不匹配的文件名
`-m NUM` `--max-count=NUM`
	仅查找满足的NUM行数据， 如果是指定多个文件， 每个文件均会按NUM行输出。
	类似的`-v`是不匹配的NUM行
`-o` `--only-matching`
	仅输出匹配部分，普通查找时意义不大，但正则匹配时比较有用
	和`-v`一起用时。。无输出
`-q` `--quiet`  `--silent`
	正常执行搜索匹配逻辑，但没有输出信息。匹配到时以0退出，未匹配时非0退出
	可通过echo $? 访问返回值，shell 脚本中可用到
`-s` `--no-messages`
	文件不存在、不可访问时。不输出错误信息。 可以采用重定向标准错误 `2>/dev/null`获取更多的兼容性
	类似 可以通过echo $? 访问返回值
`-H` `--with-filename`
	显示匹配的文件名，当搜索多个文件名时默认启用
`-h` `--no-filename`
	与`-H`相反，当搜索一个文件时默认启用
`-b` `--byte-offset`
	匹配项与文件头部的字节偏移量，如果指定`-o`则是匹配项自身，如果没有`-o` 则是行与头部的偏移
```bash
$cat abc
abc
$grep c abc -b
0:abc
$grep c abc -bo
2:c
```
`--label`
	这个选项有点意思，前面grep 的数据来源一般都是文件， 考虑下如果数据不是文件呢？
	比如长期运行的服务器的日志，为了节约磁盘空间，会使用tar/gz 归档为文件。 
	在一堆*.tar.gz 文件中grep时，要使用管道来tar -zxvf *.tar.gz | grep  something ，这时grep 无法获取数据来源的文件名，也就不能显示文件名了。
	而--label其实就是指定文件名的作用，参考 https://unix.stackexchange.com/questions/74894/understanding-grep-label
```bash
echo $'fubar\nbaz\nbat' | grep --label=inputfile -H a
inputfile:fubar
inputfile:baz
inputfile:bat
```
`-n` `--line-number`
	匹配内容在文件中的行号
`-T` `--initial-tab`
	和`-n` `-b`等选项配合使用时，会多输出TAB来对齐各列，格式上更好看
`-u` `--unix-byte-offsets`
	和`-b`配合使用，仅在windows 平台生效(因为其他平台都是UNIX offset 了)。我的Mac `(BSD grep) 2.5.1-FreeBSD` 的grep 没有`-u`选项
`-Z` `--null`
	正常情况下，显示文件名时，后面会跟一个分割符， 比如显示file:match-pattern 
	指定`--null`时，会使用''替换`:`

```bash
$grep  str  stropts.h  test.sh  -Zl | hexdump 
0000000 7473 6f72 7470 2e73 0068 6574 7473 732e
0000010 0068                                   
0000012
$grep  str  stropts.h  test.sh  -l | hexdump 
0000000 7473 6f72 7470 2e73 0a68 6574 7473 732e
0000010 0a68                                   
0000012
```

`-A NUM` `--after-context=NUM`
	匹配行后的NUM行也输出，与`-o` `--only-matching` 有冲突
`-B NUM` `--before-context=NUM`
	匹配行前的NUM行
`-C NUM` `-NUM` `--context=NUM`
	前后各NUM行
`-a` `--text`
	与`--binary-files=text` 相同的效果，将二进制文件当做文本文件来处理
	此种输出可能导致破坏效果。一种是控制字段导致shell 死掉， 甚至可能有特殊字符被视为command 执行。 `sudo rm  -rf`
`--binary-files=TYPE`
	grep 会读文件前几个字段，判断是否为文本，如果有特殊的，如`\0`，则视为binary 
	binary:仅提示在二进制文件中查找到匹配，不输出
	text:
	without-match:  与`-I` 选项类似， 忽略二进制文件。

`-D action` `--devices=ACTION`
	
	










