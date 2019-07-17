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
​	这个没啥好说的<br/>
​	
`-E`  `--extended-regexp`<br/>
​	使用`扩展正则表达式ERE` ，这里与`-e ` 有关    <br/>
​	`--` 开头的参数在Linux Shell 里面被称为长选项，一般会通过getopt getopt_long 函数来实现, `#include <getopt.h>`。 假定指定的匹配pattern以-开头。 shell 无法分辨传入的是选项，还是参数。 所以可以用长选项来实现<br/>
​	
`-e` `--regexp`<br/>
​	使用`-e` 时，仅能传递一个参数，当要传递多个时，可以grep -e ARG1  -e ARG2 -e ARG3 <br/>
​	
`-f` `--file`  <br/>
​	从文件中获取pattern<br/>
​	
`-F` `--fixed-strings`<br/>
​	不要识别为正则表达式， 以字面值来理解，不能和`-E` 一起用。<br/>
​	
`-i` `-y` `--ignore-case`<br/>
​	忽略大小写，都忽略了，所以输入的pattern 与文件行都忽略<br/>
​	
`-v` `--invert-match`<br/>
​	与普通的相反， 输出未匹配的内容<br/>
​	
`-w` `--word-regexp<br/>`
​	w=word，仅匹配整词。 vim 查找整词时，一般用`/\<word\>`<br/>
​	
`-G` `--basic-regexp`<br/>
​	基础正则表达式，默认<br/>
​	
`-P` ` --perl-regexp`<br/>
​	使用Perl 正则表达式语法<br/>
​	试图查找TAB \t 时， 可以尝试 grep -P '\t' file 。 <br/>
​	由于使用了perl 语法，其中有`$`时，也要`\$`<br/>
​	也可以尝试 `grep  $"\t" file`  来匹配TAB <br/>
​	
`-x` `--line-regexp`<br/>
​	整行匹配，文件头尾有空格时，是匹配不到的，可以考虑用sed 来做替换<br/>
​	
`-c` `--count`<br/>
​	输出匹配的个数，不输出普通的匹配文本<br/>
​	
`--color=[WHEN]` `--colour=[WHEN]<br/>`
​	never:不带颜色<br/>
​	always: 任何情况下都输出颜色控制，通过管道处理时，可能会有问题。<br/>
​	auto:	仅当输出终端时带颜色<br/>
​	颜色配置与环境环境  GREP_COLOR  GREP_OPTIONS 相关<br/>
​	
`-l` `--files-with-matches`<br/>
​	匹配pattern 的文件名<br/>
​	
`-L` `--files-without-match<br/>`
​	与`-l` 相反， 不匹配的文件名<br/>
​	
`-m NUM` `--max-count=NUM<br/>`
​	仅查找满足的NUM行数据， 如果是指定多个文件， 每个文件均会按NUM行输出。<br/>
​	类似的`-v`是不匹配的NUM行<br/>
​	
`-o` `--only-matching`<br/>
​	仅输出匹配部分，普通查找时意义不大，但正则匹配时比较有用<br/>
​	和`-v`一起用时。。无输出<br/>
​	
`-q` `--quiet`  `--silent`<br/>
​	正常执行搜索匹配逻辑，但没有输出信息。匹配到时以0退出，未匹配时非0退出<br/>
​	可通过`echo $?` 访问返回值，shell 脚本中可用到<br/>
​	
`-s` `--no-messages`<br/>
​	文件不存在、不可访问时。不输出错误信息。 可以采用重定向标准错误 `2>/dev/null`获取更多的兼容性<br/>
​	类似 可以通过`echo $?` 访问返回值<br/>
​	
`-H` `--with-filename`<br/>
​	显示匹配的文件名，当搜索多个文件名时默认启用<br/>
​	
`-h` `--no-filename`<br/>
​	与`-H`相反，当搜索一个文件时默认启用<br/>
​	
`-b` `--byte-offset`<br/>
​	匹配项与文件头部的字节偏移量，如果指定`-o`则是匹配项自身，如果没有`-o` 则是行与头部的偏移<br/>
```bash
$cat abc
abc
$grep c abc -b
0:abc
$grep c abc -bo
2:c
```

`--label`
​	这个选项有点意思，前面grep 的数据来源一般都是文件， 考虑下如果数据不是文件呢？<br/>
​	比如长期运行的服务器的日志，为了节约磁盘空间，会使用tar/gz 归档为文件。 <br/>
​	在一堆*.tar.gz 文件中grep时，要使用管道来tar -zxvf *.tar.gz | grep  something ，这时grep 无法获取数据来源的文件名，也就不能显示文件名了。<br/>
​	而--label其实就是指定文件名的作用，参考 https://unix.stackexchange.com/questions/74894/understanding-grep-label
<br/>
```bash
echo $'fubar\nbaz\nbat' | grep --label=inputfile -H a
inputfile:fubar
inputfile:baz
inputfile:bat
```

`-n` `--line-number`
​	匹配内容在文件中的行号<br/>
​	
`-T` `--initial-tab`
​	和`-n` `-b`等选项配合使用时，会多输出TAB来对齐各列，格式上更好看<br/>
​	
`-u` `--unix-byte-offsets`
​	和`-b`配合使用，仅在windows 平台生效(因为其他平台都是UNIX offset 了)。我的Mac 	`(BSD grep) 2.5.1-FreeBSD` 的grep 没有`-u`选项<br/>
​	
`-Z` `--null
​	正常情况下，显示文件名时，后面会跟一个分割符， 比如显示file:match-pattern <br/>
​	指定`--null`时，会使用''替换`:`
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

`-A NUM` `--after-context=NUM`<br/>
​	匹配行后的NUM行也输出，与`-o` `--only-matching` 有冲突<br/>
​	
`-B NUM` `--before-context=NUM`<br/>
​	匹配行前的NUM行<br/>
​	
`-C NUM` `-NUM` `--context=NUM`<br/>
​	前后各NUM行<br/>
​	
`-a` `--text`
​	与`--binary-files=text` 相同的效果，将二进制文件当做文本文件来处理<br/>
​	此种输出可能导致破坏效果。一种是控制字段导致shell 死掉， 甚至可能有特殊字符被视为command 执行。 `sudo rm  -rf`<br/>
​	
`--binary-files=TYPE`<br/>
​	grep 会读文件前几个字段，判断是否为文本，如果有特殊的，如`\0`，则视为binary <br/>
​	binary:仅提示在二进制文件中查找到匹配，不输出<br/>
​	text:<br/>
​	without-match:  与`-I` 选项类似， 忽略二进制文件。<br/>
​	
`-D action` `--devices=ACTION`<br/>
​	`everything is a file ` 考虑到这一点，如果在一个目录下有socket/device 时， grep 采用何用方法处理<br/>
​	read:  在Mac 上用/dev/random 来做测试， 结果忘了关。cpu风扇响了一下午。。。<br/>
​	skip:<br/>
​	
`-d` `--directories=ACTION`<br/>
​	read/skip 同上。 recurse 会递归在目录中查找， 与`-r` 类似
​	
`--exclude=GLOB`
​	排除掉匹配GLOB的文件 ， GLOB支持通配符`* ? [...] ` 以及使用`\` 转义

`--exclude-from=FILE`
​	FILE里面包括多个GLOB
​	
`--exclude-dir=DIR`
​	排除目录， 比如排除掉`.git` `.svn` 
​	
`-I`
​	同` --binary-files=without-match ` 二进制视为未匹配
​	
`--include=GLOB`
​	与`--exclude=GLOB` 含义相反， 仅搜索满足GLOB的文件
​	
`-R, -r, --recursive`
​	递归查找当前目录及其子目录的文件 
​	
`--line-buffered`
​	全缓存、行缓存、无缓存其中的行缓存，对性能有影响 
​	
`--mmap`
​	默认情况下使用read读取文件内容，指定此选项时，使用mmap 系统调用将文件映射到grep 的地址空间，这样性能更佳。但如果文件变小，原映射的位置将发生变化， grep 可能会coredump 
​	
`-U  --binary `
​	与`-a` `--text` 相反。 DOS & Windows系统中，grep 读取文件的前32KB 数据，以此来判断文件类型(二进制，纯文本)。grep 会去除`CR`，确保数据按行处理。 Win系统使用`CRLF`,Linux 使用`LF` .指定此选项时，将跳过这一过程，按原始字节做处理。

`-z, --null-data`
​	语义上与`-Z,--null` 相通，但作用于输入源数据。 使用`'\0'`来做为行的分割
​	
`grep中的正则表达式`
​	参考https://www.tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_01.html <br/>
​	正则表达式是啥就不多说(普通的能看，复杂的头疼)。grep 可处理`basic`, `extened`, `perl` 三种正则表达式。具体与实现有关。一般Linux 上是GNU grep  (-e -E 有转义符的区别 在-e 中使用`\|` 在-E 中使用`|`)<br/>
​	中括号表达式, `[abc123]`, 匹配出现在[]中的任意字符(字母+数字+下划线)，如果以`^`开头，则取反。在中括号中，`]` `^` `-` 会部分失去其特殊的含义  `]` 随便放， `^`不要放在头部， `-` 放在末尾（实际测试，放在头部也没事）<br/>
​	连接符， `-` 区间表达式，在两者之间的所有字符均用于匹配。  字符间的顺序与当前系统的本地化设置有关(字典序)。LC_ALL=C 是最基本的(基于ASCII的字典序)，中文环境一般不受影响 
​	有以下预定义串<br/>
​	`[[:alnum:]]`  与 ` [0-9A-Za-z]` 相同， 表示字母+数字<br/>
​	`[[:alpha:]]`  所有字母<br/>
​	`[[:cntrl:]` 控制字符<br/>
​	`[[:digit:]]`  数字<br/>
​	`[[:graph:]]`  非空，无空格<br/>
​	`[[:lower:]]` 小写<br/>
​	`[[:print:]]` 非空，包括空格<br/>
​	`[[:punct:]]` 标点<br/>
​	`[[:space:]]` 空格，新行，制表TAB<br/>
​	`[[:upper:]]`  大写<br/>
​	`[[:xdigit:]]` 十六进制[0-9a-bA-B]<br/>
​	
​	`^` 匹配行首
​	`$` 匹配行尾
​	`\<` 匹配词前的空串， `\>` 匹配词后的空串
​	`\b` 词锁定  `\bword\b` 类似于`\<word\>`
​	`\B` 与`\b` 相反，不匹配的
```bash
~$grep '\bdo\b' test.sh 
do 
~$grep '\bdo\B' test.sh 
done
~$grep '\bdo' test.sh 
do 
done
```
	`\w`  与[[:album:]]相同
	`\W`  与[^[:album:]]相同
	
	模式次数
	`?`  最多一次
	`*`  任意次
	`+`  最少一次
	`{n}` n次
	`{n,}` >=n次
	`{,m}` <=m次
	`{n,m}`  n~m 次
	正在表达式支持并(Concatenation  )，或(Alternation)

`一些括号的英文表示 `
http://www.cis.upenn.edu/~matuszek/General/JavaSyntax/parentheses.html
​	句点 `.` period<br/>
​		小括号 `()` parentheses<br/>
​	中括号 `[]` Brackets ("square brackets")<br/>
​	大括号 `{}` Braces ("curly braces")  => curly=卷曲的	<br/>
​	补注号？`^` caret <br/>
​	连字号 `-` hyphen<br/>
​	竖线  `|`  infix



`brace expansion` 

​	花括号展开 

​	https://blog.csdn.net/astrotycoon/article/details/50886676

​	https://www.gnu.org/software/bash/manual/html_node/Brace-Expansion.html




`readline` 

​	这个库很有意思，很多cli 都用到了。 在cli 里面出现`^[[A^H` 一般就是由于readline 配置不当导致。



`set -o ignoreeof`

​	shell 读到ctrl+D 时，不会退出。