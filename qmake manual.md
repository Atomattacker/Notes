# qmake
qmake是一个帮助简化跨平台项目构建的工具。qmake只需少量的几行信息就可以自动生成Makefile,可用于任何的软件项目而不仅仅只是Qt的项目。  

qmake基于工程文件生成Makefile,针对Qt平台，qmake支持Qt工程中的moc和uic的构建规则，同时也支持生成 Microsoft Visual studio的工程文件。

## qmake工程文件
qmake使用的工程文件支持简单和相当复制的构建系统。在工程文件中，通过变量来保存字符串，这些变量通知qmake使用不同的配置选项或者通过构建过程中所需文件名和路径。  

qmake读取工程文件，并根据其内容生成Makefile。比如HEADERS和SOURCES变量用于告诉qmake同目录下头文件和源文件。

给变量赋值的方式如下：
```
HEADERS = mainwindow.h paintwidget.h
```
变量的赋值只占一行，如果需要夸多行使用\:
```
SOURCES = main.cpp mainwindow.cpp\
          paintwidget.cpp
CONFIG += qt
```

一些常用的qmake可以识别的变量：
|变量|内容|
|----|---|
|CONFIG|工程的通用配置选项|
|DESTDIR|可执行文件或二进制文件生成后放置的目录|
|FORMS|uic处理的UI文件|
|HEADERS|构建所需的头文件|
|QT|Qt特定的配置选项|
|RESOURCES|.rc资源文件|
|SOURCES|构建所需的源文件|
|TEMPLATE|工程模版，决定构建的输出是应用程序、库还是插件|

### 变量内容
变量的内容可以通过$$获取,可以将获取的内容赋给另一个变量：
```
TEMP_SOURCES=$$SOURCES
```
### 空格
工程文件中的空格一般用来分隔变量值的，如果变量值中包含空格，必须使用双引号：
```
DEST="Program Files"
```
### 注释
工程文件中的注释是以#开头到行尾,比如：
```
# comments usually start at the beginning of a line,but
# they can also follow other content on the same line.
```
如果在变量赋值中需要用到#,必须使用内建的变量**LITERAL_HASH**。

### 内建函数和控制流
qmake提供一下内建的函数对变量内容进行处理，比如**include**函数使用文件名作为参数，用于将文件内容包含到当前函数放置的位置
```
include(other.pro)
```
条件结构类似if语句
```
win32{
    SOURCES +=paintwidget_win.cpp
}
```
上面花括号中的赋值操作只在条件为true时使用，只有设置了win32变量，条件才为true,在windows平台，这个变量自动设置，也可以在其他平台上通过运行qmake附带-win32的命令选项来应用。
**左花括号必须和条件处于同一行**  

内建的for函数可以对变量值集合进行迭代，下面的代码将存在的目录添加到变量SUBDIRS中：
```
EXTRAS = handlers tests docs
for(dir, EXTRAS){
    exist($$dir){
        SUBDIRS += $$dir
    }
}
```

### 工程模版
变量TEMPLATE用于定义工程构建类型，如果不在工程文件中定义，qmake默认认为是构建应用程序类型。
工程模版的类型包括：
|模版|qmake产出描述|
|----|------------|
|app|默认类型，生成Makefile构建一个应用程序|
|lib|生成Makefile构建一个库|
|subdirs|生成Makefileb包含SUBDIRS指定的子目录|
|vcapp|生成VS工程文件构建一个应用程序|
|vclib|生成VS工程文件构建一个库|
|vcsubdirs|生成VS解决方案包含子目录工程|

### 通用配置
变量CONFIG指定编译器或链接器连接时的选项，CONFIG变量可以添加任何东西，但下面的值是qmake可以识别的：
|选项|描述|
|----|----|
|release|工程在release模式下构建，如同时指定了debug,此项被忽略|
|debug|工程在debug模式下构建|
|debug_and_release|工程既在debug模式下构建，也在release模式下构建|
|debug_and_release_target|工程既在debug模式下构建，也在release模式下构建，TARGET构建到debug和release目录下|
|build_all|如果debug_and_release被指定，工程既在debug模式下构建，也在release模式下构建|
|autogen_precompile_source|自动生成一个.cpp文件包含.pro中指定的预编译的头文件|
|ordered|如果使用的是subdirs模版，指定该选项表示按照子目录指定的顺序处理|
|warn_on|尽可能多的输出警告信息，如果指定了warn_off，该选项被忽略|
|warn_off|尽可能少的输出警告信息|
|copy_dir_files|允许安装规则复制目录而不仅仅是文件|

指定debug_and_release会同时构建debug和release版本，qmake生成的Makefile包含构建两个版本的规则，通过下面的调用可以构建两个版本：
```
make all
```
变量CONFIG指定的选项可以作为条件判断，可以使用内建的CONFIG()函数进行测试判断，下面的例子判断是否使用opengl选项：
```
CONFIG(opengl){
    message(Building with OpenGL support.)
 } else {
    message(OpenGL support is not available.)
 }
```
下面这些选项定义构建的项目类型，一些选项只在相关平台上起作用，其他平台不起作用：
|选项|描述|
|----|---|
|qt|Qt应用程序项目，应该链接Qt库。可以使用变量QT控制应用程序需要的Qt模块|
|thread|多线程应用程序项目|
|x11|X11应用程序或库工程|

### Qt库声明
如果CONFIG变量包含选项qt，qmake启用对Qt应用程序的支持，可以使用变量QT控制启用哪些Qt模块，比如使用xml和network模块：
```
CONFIG +=qt
QT += network xml
```
变量QT默认包含core和gui模块

### 配置特征
qmake可以在特征文件(.prf)中指定额外的特征。这些额外特征通常在构建过程中对自定义的工具进行支持，通过在CONFIG变量中加入需要添加的特征名称，加入到构建过程中。
```
CONFIG += link_pkgconfig
PKGCONFIG += ogg dbus-1
```
### 声明其他的库
如果需要使用Qt提供的库之外的库，需要在工程文件中指定。
在变量LIBS中添加库的搜索路径和库文件:
```
LIBS += -L/usr/local/lib -lmath
```

## qmake命令行选项
**用法**
```
qmake [mode] [options] files
```
qmake支持两种模式，默认模式qmake使用工程文件生成Makefile，qmake也可以生成工程文件，mode可以是下面两种之一：
- -makefile - 输出Makefile
- -project - 输出工程文件

**选项**
qmake支持一大串选项用于定制构建过程或覆盖默认设置：
- -help   
  提供qmake的有用的帮助信息。
- -o file   
  将输出重定向到file。如果没指定该选项， qmake根据运行的模式生成合适的输出文件，如果指定为"-"，输出重定向到标准输出。 
- -d   
  输出debug信息。

如果需要为不同目标平台构建不同的工程，可以指定下面的选项，设置相应平台变量：
- -unix  
  qmake将运行在unix模式，该模式下，unix文件命名和路径规则将被使用，同时unix条件测试将成功。这是所有unix平台上的默认模式。
- -macx  
  qmake将运行在Mac OS X模式，该模式下，unix文件命名和路径规则将被使用，同时macx条件测试将成功。这是Mac OS X平台上的默认模式。
- -win32  
  qmake将运行在win32模式，该模式下，Windows文件命名和路径规则将被使用，同时win32条件测试将成功。这是Windows平台上的默认模式。  

模版通常在工程文件中的TEMPLATE变量中指定，可以通过下面的选项覆盖或修改：
- -t tmpl   
  qmake将TEMPLATE变量的值使用tmpl覆盖，但是只在.pro文件处理后覆盖。
- -tp prefix  
  qmake将prefix添加到变量TEMPLATE中

警告信息级别可以通过下面的选项调整：
- -Wall   
  qmake将报告所有已知的警告。
- -Wnone  
  不生成警告信息。
- -Wparser   
  qmake将只生成解析警告，这些警告通常提醒你解析工程文件时的陷阱和潜在的问题。
- -Wlogic
  qmake将警告工程文件的陷阱和潜在问题。

**Makefile模式选项**  
```
qmake -makefile [options] files
```

- -after   
  qmake将在处理完指定的文件之后再处理命令行中的赋值
- -nocache  
  qmake将忽略.qmake.cache文件。
- -nodepend   
  qmake将不生成任何依赖信息。
- -cache file  
  qmake将使用file作为cache文件，忽略其他.qmake.cache文件。
- -spec spec  
  qmake将使用spec作为平台路径和编译信息，QMAKESPEC的值被忽略。
  
可以在qmake命令行中指定赋值表达式,它将在工程文件处理之前执行：
```
qmake -makefile -unix -o Makefile "CONFIG+=test" test.pro
```
如果确定赋值表达式在处理工程文件之后执行，使用-after选项。

**Project模式选项**
```
qmake -project [options] files
```

- -r  
  qmake将递归查找指定的目录
- -nopwd  
  qmake将不在当前工作目录下查找源文件，而只使用指定文件files

在该模式下，如果files指定的包含目录，目录将会添加到DEPENDPATH变量中，如果指定的是文件，或根据文件的拓展名，添加到正确的变量中，比如UI文件会添加到FORMS变量中，C++源文件添加到SOURCES中。

## qmake高级使用
很多qmake工程文件使用name=value和name += value的形式定义。qmake还提供其他的操作符，函数和条件判断。

### 操作符
=操作符将值赋给变量：
```
TARGET = myapp
```
=操作符将覆盖掉变量之前设置的值。

+=操作符将新值添加到变量的值列表中：
```
DEFINES += QT_DLL
```

-=操作符将值从变量的值列表中移除：
```
DEFINES -= QT_DLL
```
 *=操作符在值不包含在变量值列表中时加入中列表中，这个操作符可以防止变量值多次加入:
 ```
DEFINES *= QT_DLL
 ```

~=操作符替换匹配正则表达式的值：
```
DEFINES ~= s/QT_[DT].+/QT
```
上面将值列表中所有以QT_D或QT_T开头的值替换成QT.

$$操作符用于提取变量的内容：
```
EVERYTHING = $$SOURCES $$HEADERS
message("The project contains the following files:")
message($$EVERYTHING)
```

### Scopes
Scopes类似编程语言中的if语句，如果条件为true，scope中的声明被处理

**语法**
```
<condition>{
  <command or definition>
  ...
}
```
左花括号必须与condition写在同一行
```
win32{
  SOURCES += paintwidget_win.cpp
}
```
如果希望在非Windows平台应用设置，可以使用逻辑反条件:
```
!win32{
  SOURCES -= paintwidget_win.cpp
}
```
Scopes可以嵌套使用：
```
macx{
  debug{
    HEADERS += debugging.h
  }
}
```

可以使用:操作符，简化嵌套的Scopes：
```
macx:debug{
  HEADERS += debugging.h
}
```
单个条件也可以使用:操作符：
```
win32:DEFINES += QT_DLL
```
|操作符类似逻辑或操作符，只需要一个条件为true就可：
```
win32|macx{
  HEADERS += debugging.h
}
```

也可以使用else提供选择分支,也可以结合:操作符一起使用：
```
win32:xml{
  message(Building for Windows)
  SOURCES += xmlhandler_win.cpp
} else:xml{
  SOURCES +=xmlhandler.cpp
} esle {
  message("Unknown configuration")
}
```

存储在变量CONFIG中的值会被qmake特殊对待，每一个可能的值都可以作为scope的条件，比如，下面的例子将opengl加入CONFIG,对opengl的判断结果将会是true,
opengl的scope将会被处理：
```
CONFIG += opengl

opengl{
  TARGET = application-gl
} else{
  TARGET =application
}

```

除了win32，macx和unix这些作为scope的条件外，其他内建的平台值和编译器指定的值也可以作为条件测试，这些值由Qt的mkspecs目录指定，下面的例子测试linux-g++是否指定：
```
message($$QMAKESPEC)
linux-g++{
  message(Linux)
}
```
可以测试mkspecs目录下存在的所有指定的规格。

### 变量
工程文件中使用的变量多是qmake用于生成Makefile用的，你也可以创建自己的变量，比如：
```
MY_VARIABLE = value
```
对于自定义的变量没有限制，qmake将会忽略这些变量，除非需要计算这些变量的值，可以使用$$来获取变量的值，并赋给另外的变量：
```
MY_DEFINES =$$DEFINES
```
也可以使用下面的写法：
```
MY_DEFINES = $${DEFINES}
```
第二种写法允许将变量值和另外的值结合起来，比如：
```
TARGET = myproject_$${TEMPLATE}
```
























