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
**Q_OBJECT**宏是使用Qt信号和槽机制以及Qt对象服务必须声明的宏。Qt文档给出该宏必须声明在类定义的private区域，并且要使用该宏必须是QObject的直接子类或间接子类。Q_OBJECT宏的定义如下：
```C++

#define Q_OBJECT_CHECK \
    template <typename T> inline void qt_check_for_QOBJECT_macro(const T &_q_argument) const \
    { int i = qYouForgotTheQ_OBJECT_Macro(this, &_q_argument); i = i; }

#ifdef Q_NO_DATA_RELOCATION
#define Q_OBJECT_GETSTATICMETAOBJECT static const QMetaObject &getStaticMetaObject();
#else
#define Q_OBJECT_GETSTATICMETAOBJECT
#endif


#ifndef QT_NO_TRANSLATION
# ifndef QT_NO_TEXTCODEC
// full set of tr functions
// ### Qt 5: merge overloads
#  define QT_TR_FUNCTIONS \
    static inline QString tr(const char *s, const char *c = 0) \
        { return staticMetaObject.tr(s, c); } \
    static inline QString trUtf8(const char *s, const char *c = 0) \
        { return staticMetaObject.trUtf8(s, c); } \
    static inline QString tr(const char *s, const char *c, int n) \
        { return staticMetaObject.tr(s, c, n); } \
    static inline QString trUtf8(const char *s, const char *c, int n) \
        { return staticMetaObject.trUtf8(s, c, n); }
# else
// no QTextCodec, no utf8
// ### Qt 5: merge overloads
#  define QT_TR_FUNCTIONS \
    static inline QString tr(const char *s, const char *c = 0) \
        { return staticMetaObject.tr(s, c); } \
    static inline QString tr(const char *s, const char *c, int n) \
        { return staticMetaObject.tr(s, c, n); }
# endif
#else
// inherit the ones from QObject
# define QT_TR_FUNCTIONS
#endif

//Q_OBJECT宏
#define Q_OBJECT \
public: \
    Q_OBJECT_CHECK \
    static const QMetaObject staticMetaObject; \
    Q_OBJECT_GETSTATICMETAOBJECT \
    virtual const QMetaObject *metaObject() const; \
    virtual void *qt_metacast(const char *); \
    QT_TR_FUNCTIONS \
    virtual int qt_metacall(QMetaObject::Call, int, void **); \
```
总的来说，Q_OBJECT宏声明了一些函数，其中包含Q_OBJECT的头文件由moc处理之后会生成相应的moc_*.cpp文件，该文件中是对Q_OBJECT宏声明的函数的实现。同时该文件中也对声明的信号函数进行了实现。

