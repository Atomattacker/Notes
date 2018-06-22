# 源文件与程序(Source Files and Programs)

## 分离编译
文件是传统的存储单元和编译单元。  
用户提供源文件给编译器，然后文件经过预处理（处理#include指令和宏等），得到的结果称为翻译单元(translation unit)。这个翻译单元是编译器真正处理的，并且是C++语言规则的描述。  
链接器(linker)是将分离的编译部分绑在一起的程序。  
以源文件的方式组织程序通常叫做程序的物理结构(physical structure)。对程序进行物理上的分离，放在分立的源文件中。 

## 连接
一个名字如果可以用在它定义所在的翻译单元之外的翻译单元中，那么就说它具有外部连接(external linkage)。  
一个名字如果只能在其定义的翻译单元中引用，那就说它具有内部连接(internal linkage)。 一个inline函数必须在每一个使用该函数的翻译单元中有一致的定义。下面的示例是不合法的：
```C++
//file1.c:
inline int f(int i){return i;}

//file2.c:
inline int f(int i){return i+1;}
```
下面的结合外部连接和inline的例子也是错误的：
```C++
//file1.c:
extern inline int g(int i);
int h(int i){return g(i);} //错误:g()在本翻译单元中未定义

//file2.c:
extern inline int g(int i){return i+1;}
```
默认情况下，const和typedef具有内部连接，因此下面的示例虽然使人迷惑，但却是合法的：
```C++
//file1.c:
typedef int T;
const int x = 7;

//file2.c:
typedef void T;
const int x = 8;
```
只在一个翻译单元中使用的全局变量往往使人迷惑，最好避免。为保存一致性，应该在只在头文件中加上全局const和inline。

const可以显示声明成外部连接的：
```C++
//file1.c:
extern const int a = 77;

//file2.c:
extern const int a;

void g()
{
    cout << a << '\n'; //will print 77
}
```

无名名字空间(unnamed namespace)可以让一个名字(name)成为翻译单元的局部(local)名字。无名名字空间效果类似内部连接：
```C++
//file1.c:
namespace{
    class X{ /* ... */ };
    void f();
    int i;
    //...
}

//file2.c:
class X{/* ... */};
void f();
int i;
//...
```
C和旧式C++使用关键字static来声明内部连接。除了函数内和类内，不要使用static。(注:B.S的建议)

## 程序
程序是由链接器将分离编译的单元组合成的一个集合。程序必须包含且只能包含一个main()函数。程序的主要作用的是调用这个main函数，等其调用结束。main函数返回的int型值作为程序的结果。

### 非局部变量的初始化
原则上，定义在任何函数之外的变量会在main函数调用之前初始化。这些非局部变量在其翻译单元中按照它们的声明顺序初始化。如果这些变量没有显示指定初始化器，它们会采用对应类型的默认初始化。对于内置类型和枚举初始化值是0。  
不同翻译单元的全局变量的初始化顺序不能保证。  
全局变量的初始化器抛出的异常无法捕捉。  
