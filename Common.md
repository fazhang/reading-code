##Common/StringUtils.h 

###1、type_traits  
类型萃取，在模板中使用
例 
```c++
/// Given an integer, return the adequate suffix for
/// printing an ordinal number.
template <typename T>
std::string getOrdinalSuffix(T n)
{
    static_assert(std::is_integral_v<T> && std::is_unsigned_v<T>,
        "Unsigned integer value required")
}
```
###2、isascii
```c++
/// More efficient than libc, because doesn't respect locale. But for some functions table implementation could be better.
inline bool isASCII(char c)
{
    return static_cast<unsigned char>(c) < 0x80;
}
```
来自glibc 的实现 
https://github.com/lattera/glibc/blob/master/ctype/ctype.h
```c++
#define	__isascii(c)	(((c) & ~0x7f) == 0)	/* If C is a 7 bit value.  */
```
哪里能看出来 `More efficient than libc` 了？ 和本地化也没关系呀？

###3、murmurhash
一统天下的hash 算法
http://calvin1978.blogcn.com/articles/murmur.html
https://dadario.com.br/cryptographic-and-non-cryptographic-hash-functions/
https://www.sderosiaux.com/articles/2017/08/26/the-murmur3-hash-function--hashtables-bloom-filters-hyperloglog/
https://www.flyml.net/2016/09/05/cassandra-tutorial-murmurhash/

如何找到下方的magic number 
https://sites.google.com/site/murmurhash/discussion
```c++
inline   intHash64(  x)
{
    x ^= x >> 33;
    x *= 0xff51afd7ed558ccdULL;
    x ^= x >> 33;
    x *= 0xc4ceb9fe1a85ec53ULL;
    x ^= x >> 33;

    return x;
}
```
然后seahash  宣称比murmur 更快
https://docs.rs/seahash/3.0.6/seahash/

###4、指数算法
musl libc 库  。 //知道有很多libc 库， 才知道有这么多
