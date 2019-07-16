##Server主流程

###main
registerFunctions();
//一大段不太明显的函数注册？
//zookeeper  ，暂时忽略
getMemoryAmount();  通过sysconf 获取物理内存大小
http://man7.org/linux/man-pages/man3/sysconf.3.html

mlockall 系列函数， 锁定进程内存， 防止被交换出去。
https://linux.die.net/man/3/mlockall

