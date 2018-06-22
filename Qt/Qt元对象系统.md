# Qt元对象系统
Qt的元对象系统提供了对象间通讯的信号和槽机制，运行时类型信息和动态属性系统。元对象系统基于3样东西：
1. QObject作为使用元对象系统的类的基类
2. 宏Q_OBJECT声明在类的private区域，提供了动态属性，信号，槽等元对象特性。
3. 元对象编译器(moc)生成必要的代码，实现元对象特性。

moc工具读取C++源文件，如果发现文件中含有Q_OBJECT宏，它会生成另一个包含元对象代码C++源文件。

## Qt元对象系统的类型
Qt元对象系统由下面几个核心类组成：
- **QMetaObject** QMetaObject包含了Qt对象的元信息(meta-information)。
- **QMetaType** QMetaType管理元对象系统中的命名类型。
- **QMetaProperty**  QMetaProperty提供属性的元数据。
- **QMetaClassInfo**  QMetaClassInfo附带类的额外信息。
- **QMetaEnum** QMetaEnum包含枚举元数据。
- **QMetaMethod** QMetaMethod包含成员函数的元数据。

## 元对象系统的源码
元对象系统的几个类QMetaMethod、QMetaEnum、QMetaProperty、QMetaClassInfo定义在头文件qmetaobject.h中，QMetaObject定义在qobjectdefs.h中，QMetaType定义在与其类名相符的qmetatype.h文件中。

QMetaObject对象实例通常是在继承了QObeject的子类中创建，该对象实例存储了所有的关于子类的元信息，可以通过QObject::metaObject()获得该实例(需要声明Q_OBJECT宏)。

## 信号与槽机制
信号与槽是Qt中供对象交流通讯的的机制，要使用信号与槽机制，首先包含信号和槽函数的类必须是QObject的子类或间接子类，其次需要在子类的private域声明Q_OBJECT宏。声明信号使用宏signals，声明槽函数使用slots。  
Qt中对于宏signals定义成protected, slots宏定义为空:
```
#define slots
#define signals protected
```

signals声明的信号函数只需要声明，不需要实现，其实现由moc生成。

对于如下类：
```C++
class SignalSlot : public QObject
{
    Q_OBJECT

public:
    SignalSlot();


signals:
    void mysignal1(int);

    void mysignal2(const SignalSlot &);


private slots:
    void slotFunc1(int);
};
```
宏展开之后是标准的C++程序
```C++
/* signalslot.h */
class SignalSlot : public QObject
{

public:
//Q_OBJECT_CHECK 
    template <typename T> 
    inline void qt_check_for_QOBJECT_macro(const T &_q_argument) const 
    { 
        int i = qYouForgotTheQ_OBJECT_Macro(this, &_q_argument); 
        i = i + 1; 
    }

    static const QMetaObject staticMetaObject;  
    
    virtual const QMetaObject *metaObject() const; 
    virtual void *qt_metacast(const char *);     
    virtual int qt_metacall(QMetaObject::Call, int, void **); 

//QT_TR_FUNCTIONS
    static inline QString tr(const char *s, const char *c = 0) \
        { return staticMetaObject.tr(s, c); } \
    static inline QString trUtf8(const char *s, const char *c = 0) \
        { return staticMetaObject.trUtf8(s, c); } \
    static inline QString tr(const char *s, const char *c, int n) \
        { return staticMetaObject.tr(s, c, n); } \
    static inline QString trUtf8(const char *s, const char *c, int n) \
        { return staticMetaObject.trUtf8(s, c, n); }

public:
    SignalSlot();

//signals:
protected:
    void mysignal1(int);

    void mysignal2(const SignalSlot &);

//private slots:
private:
    void slotFunc1(int);
};
```
moc处理C++源文件后生成moc_*.cpp的源文件，这个源文件中对Q_OBJCET声明的函数和信号函数进行了实现，生成的源文件大致如下所示：
```C++
/* moc_signalslot.cpp */

static const uint qt_meta_data_SignalSlot[] = {

 // content:
       6,       // revision
       0,       // classname
       0,    0, // classinfo
       3,   14, // methods
       0,    0, // properties
       0,    0, // enums/sets
       0,    0, // constructors
       0,       // flags
       2,       // signalCount

 // signals: signature, parameters, type, tag, flags
      12,   11,   11,   11, 0x05,
      27,   11,   11,   11, 0x05,

 // slots: signature, parameters, type, tag, flags
      49,   11,   11,   11, 0x08,

       0        // eod
};

static const char qt_meta_stringdata_SignalSlot[] = {
    "SignalSlot\0\0mysignal1(int)\0"
    "mysignal2(SignalSlot)\0slotFunc1(int)\0"
};

void SignalSlot::qt_static_metacall(QObject *_o, QMetaObject::Call _c, int _id, void **_a)
{
    if (_c == QMetaObject::InvokeMetaMethod) {
        Q_ASSERT(staticMetaObject.cast(_o));
        SignalSlot *_t = static_cast<SignalSlot *>(_o);
        switch (_id) {
        case 0: _t->mysignal1((*reinterpret_cast< int(*)>(_a[1]))); break;
        case 1: _t->mysignal2((*reinterpret_cast< const SignalSlot(*)>(_a[1]))); break;
        case 2: _t->slotFunc1((*reinterpret_cast< int(*)>(_a[1]))); break;
        default: ;
        }
    }
}

const QMetaObjectExtraData SignalSlot::staticMetaObjectExtraData = {
    0,  qt_static_metacall 
};

const QMetaObject SignalSlot::staticMetaObject = {
    { &QObject::staticMetaObject, qt_meta_stringdata_SignalSlot,
      qt_meta_data_SignalSlot, &staticMetaObjectExtraData }
};

#ifdef Q_NO_DATA_RELOCATION
const QMetaObject &SignalSlot::getStaticMetaObject() { return staticMetaObject; }
#endif //Q_NO_DATA_RELOCATION

const QMetaObject *SignalSlot::metaObject() const
{
    return QObject::d_ptr->metaObject ? QObject::d_ptr->metaObject : &staticMetaObject;
}

void *SignalSlot::qt_metacast(const char *_clname)
{
    if (!_clname) return 0;
    if (!strcmp(_clname, qt_meta_stringdata_SignalSlot))
        return static_cast<void*>(const_cast< SignalSlot*>(this));
    return QObject::qt_metacast(_clname);
}

int SignalSlot::qt_metacall(QMetaObject::Call _c, int _id, void **_a)
{
    _id = QObject::qt_metacall(_c, _id, _a);
    if (_id < 0)
        return _id;
    if (_c == QMetaObject::InvokeMetaMethod) {
        if (_id < 3)
            qt_static_metacall(this, _c, _id, _a);
        _id -= 3;
    }
    return _id;
}

// SIGNAL 0
void SignalSlot::mysignal1(int _t1)
{
    void *_a[] = { 0, const_cast<void*>(reinterpret_cast<const void*>(&_t1)) };
    QMetaObject::activate(this, &staticMetaObject, 0, _a);
}

// SIGNAL 1
void SignalSlot::mysignal2(const SignalSlot & _t1)
{
    void *_a[] = { 0, const_cast<void*>(reinterpret_cast<const void*>(&_t1)) };
    QMetaObject::activate(this, &staticMetaObject, 1, _a);
}

```
