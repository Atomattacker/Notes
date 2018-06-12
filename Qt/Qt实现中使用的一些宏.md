# Qt源码结构
Qt大多数公开的给予用户使用的类(比如QObject)的实现代码，并不是真正的实现代码，而每个用户可以直接引用的头文件(如qobject.h)对应有一个以_p结尾的(如qobject_p.h)头文件，该头文件中声明的类才是真正的功能实现类，该类通常用户使用的类加上Private作为类名，比如类QObjectPrivate。  

NOTE:以下用header代指用户使用的头文件名
header.h和header_p.h的实现源代码通常在同一个cpp文件header.cpp中，比如QObject类和QObjectPrivate类的实现都在qobject.cpp文件中。  

## Qt实现中用到的宏
通常header.h头文件中的类QtClass(代指头文件中声明的类)会使用宏Q_DECLARE_PRIVATE来声明获取其实现类QtClassPrivate的方法，而在header_p.h头文件中类QtClassPrivate会使用宏Q_DECLARE_PUBLIC声明获取类QtClass的方法。在类QtClass的实现中常会使用红Q_D来声明一个指向QtClassPrivate的指针d,而在QtClassPrivate实现中也会使用宏Q_Q来声明一个指向QtClass的指针q。
下面是这些宏的定义:
```C

template <typename T> 
static inline T *qGetPtrHelper(T *ptr) { return ptr; }

template <typename Wrapper> 
static inline typename Wrapper::pointer qGetPtrHelper(const Wrapper &p) { return p.data(); }


#define Q_DECLARE_PRIVATE(Class) \
    inline Class##Private* d_func() { return reinterpret_cast<Class##Private *>(qGetPtrHelper(d_ptr)); } \
    inline const Class##Private* d_func() const { return reinterpret_cast<const Class##Private *>(qGetPtrHelper(d_ptr)); } \
    friend class Class##Private;


#define Q_DECLARE_PUBLIC(Class)                                    \
    inline Class* q_func() { return static_cast<Class *>(q_ptr); } \
    inline const Class* q_func() const { return static_cast<const Class *>(q_ptr); } \
    friend class Class;

#define Q_D(Class) Class##Private * const d = d_func()
#define Q_Q(Class) Class * const q = q_func()   
```

## Qt元数据及信号和槽使用的宏
