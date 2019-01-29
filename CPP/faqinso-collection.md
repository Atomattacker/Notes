**Q1**. 如果在构造函数中写return语句会怎样?
```
struct A
{
    A(){return;}
}
```
上面的代码能很好的通过编译，没有任何错误，但是下面的代码无法通过编译:
```
struct A
{
    A(){return 100;}
}
```
会得到提示: 
```
error:returning a value from a constructor
```


**A**. 在构造函数中使用return语句是符合标准的。构建函数是无返回值的函数，无返回值的函数包括：void函数，构建函数和析构函数。在C++标准的6.6.3/2提到在无返回值函数中使用return语句返回一个参数是不合法的。
> **6.6.3 The return statement**   
>  
> 2  A return statement without an expression can be used only in functions that do not return a value, that is, a function with the return type void, a constructor (12.1), or a destructor (12.4). A return statement with an expression of non-void type can be used only in functions returning a value; the value of the expression is returned to the caller of the function.

此外， 12.1/12声明：
> **12.1 Constructors**  
>  
> 12 No return type (not even void) shall be specified for a constructor. A return statement in the body of a constructor shall not specify a return value.

BTW, 在C++中，void函数中return一个参数是合法的，只要参数的类型是void  
```
void foo(){
    return (void)0; //legal in C++ (but not in C)
}
```
但是上面的用法不允许用在构造函数中，因为它不是void函数。  
有另外一个相关的难理解的关于构造函数中return的使用是：在构造函数的funtion-try-block使用return是不合法的(其他函数中没问题)。  
> **15.3 Handling an exception**  
>   
> 15 If a return statement appears in a handler of the function-try-block of a constructor, the program is ill formed.

**Q2**. 为什么模版只能在头文件中实现？

**A**. 模版的实现放置在头文件中并不是必须的，当实例化一个模版，编译器根据模版参数创建一个新类，比如：
```
template<typename T>
struct Foo
{
    T bar;
    void doSomething(T param){ /* do stuff using T */ }
};

Foo<int> f;
```
编译器在编译上面的代码时，会创建一个新的类（我们姑且叫它FooInt）,类似下面这样：
```
struct FooInt
{
    int bar;
    void doSomething(int param){ /* do stuff using int */ }
};
```
因此，编译器需要访问方法的实现从而使用模版参数实例化它（在上面的代码中是int）。如果实现不在头文件中，编译器无法访问到它的实现，也就不能实例化模版。所以，一个常见的解决方法是将模版的声明在头文件中，而将实现放到一个实现文件中（比如.tpp），然后在头文件的末尾将这个实现文件包含进来
```
template<typename T>
struct Foo
{
    void doSomething(T param);
};

#include "Foo.tpp"

//Foo.tpp
template <typename T>
void Foo<T>::doSomething(T param)
{

}
```
这种方式也可以使声明和实现分开，而且编译器也可以访问。  

另一种解决方法是保持实现分离，并显示实例化所有你需要的模版实例：
```
//Foo.h
template<typename T> struct Foo{};

//---------------------------------------------------
// Foo.cpp

temlate class Foo<int>;
template class Foo<float>;

```


**Q3**. 什么时候用static_cast, dynamic_cast, const_cast, reinterpret_cast, C-风格转换(type)value和Function-style转换type(value)？

**A**. **static_cast**是你首先应该尝试的，它应用类型间的隐式转换(比如int到float，指针到void*)，也可以调用显示或隐式的转换函数，很多情况下，显示使用**static_cast**是不必要的，要知道T(something)语法和(T)something的语法是等效的，并且应该避免使用。  
**static_cast**也可以用于继承层次间的转换，向上转换（转换成基类）是不必要的，但是向下转换可以使用，只要不是使用了虚继承。它不会对转换做检查，因此，如果一个对象的实际类型不是向下转换的类型，那么**static_cast**向下转换就是未定义行为。   

**const_cast**用于移除或添加变量的const,没有其他的C++转换(C转换除外)可以移除const(reinterpret_cast也不行)。需要指明的是从形式上将实际是const的变量的const移掉是为定义的行为，如果将一个指向非const的对象的引用的const移掉，这个转换是安全的。  

**dynamic_cast**几乎是专门用于处理多态的，你可以将一个指向多态类型的指针或引用转成任何其他类类型（多态类型至少有一个自己声明的或者继承的虚函数）。**dynamic_cast**不仅可以向下转换，而且还可以向其他继承链上转换，它会查找需要的对象并返回它，如果查找不到指针就返回nullptr，引用就抛出std::bad_cast。这个转换有一些限制，在钻石继承中如果没有使用虚继承，不能使用该转换，而且只有是public继承才能使用该转换。

**reinterpret_cast**是最危险的转换，需要非常小心的使用。它直接将一种类型转换成另一种类型，比如将指针值从一种类型转成另一种，或者把指针转成int。该转换只能保证你将转换结果转换会原来的类型，你将得到相同的值(如果转换的类型比原始类型小，就得不到原来的值)。

C-风格和Function-style转换是一样的，C-风格转换被定义为依次执行下面的转换，直到转换成功：
- const_cast
- static_cast
- static_cast 然后const_cast
- reinterpret_cast
- reinterpert_cast 然后const_cast