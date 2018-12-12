# C++编译器及OS的一些预定义宏
当编写可移植的C++代码，需要做一些所依赖的编译器或操作系统的条件判断，比如下面这样：
```C++
#if defined(_MSC_VER)
// Visual Statuio 编译器特定代码
#endif
```
做这些检查需要检测编译器的一些预定义宏设置。

## OS预定义宏

### Android  
|类型|宏|格式|描述|
|-|-|-|-|
|标识|`__ANDROID__`|||
|版本|`__ANDROID_API__`|V|V = API Version, Must be included from <android/api-level.h>|

例子  
|Android版本|`__ANDROID_API__`|  
|-|-|  
|1.0|1|
|1.1|2|
|1.5|3|
|1.6|4|
|2.0|5|
|2.0.1|6|
|2.1|7|
|2.2|8|
|2.3|9|
|2.3.3|10|
|3.0|11|

### BDS环境  
|类型|宏|格式|描述|
|-|-|-|-|
|标识|`__FreeBSD__`  `__NetBSD__`  `__OpenBSD__` `__bsdi__` `__bsdi__`|||
|版本|`BSD`|YYYYMM|YYYY=年份, MM=月份, 必须包含<sys/param.h>|
|版本|`BSD4_2`  `BSD4_2`  `BSD4_2`||必须包含<sys/param.h>|
|标识|`_SYSTYPE_BSD`||DEC C定义|

### Cygwin环境
|类型|宏|
|-|-|
|标识|`__CYGWIN__`|

### FreeBSD
|类型|宏|格式|描述|
|-|-|-|-|
|标识|`__FreeBSD__`|||
|标识|`__FreeBSD_kernel__`||FreeBSD 8.3, 9.1, 10.0.1|
|版本|`BSD`|||
|版本|`__FreeBSD__`|V|V=Version|
|版本|`__FreeBSD_version`|?|必须包含<osreldate.h>|

例子  
|FreeBSD|`__FreeBSD__`|`__FreeBSD_version`|
|1.x|1||
|2.0-RELEASE|2|119411|
|2.2-RELEASE|2|220000|
|3.0-RELEASE|3|300005|
|4.0-RELEASE|4|400017|
|4.5-RELEASE|4|450000|

### GNU/Linux
|类型|宏|
|-|-|
|标识|`__gnu_linux__`|

### Linux kernel
基于Linux内核的系统定义了下面这些宏：
|类型|宏|描述|
|-|-|-|
|标识|`__linux__`|[~](http://www.faqs.org/docs/Linux-HOWTO/GCC-HOWTO.html#INDEX.25)|
|标识|`linux`|Obsolete (not POSIX compliant)|
|标识|`linux`|Obsolete (not POSIX compliant)|

### MacOS
|类型|宏|描述|
|-|-|-|
|标识|`macintosh`|Mac OS 9|
|标识|`macintosh`|Mac OS 9|
|标识|`__APPLE__ && __MACH__`|Mac OS X  Defined by GNU C and Intel C++|

### MSDOS
|类型|宏|
|-|-|
|标识|`MSDOS`|
|标识|`__MSDOS__`|
|标识|`_MSDOS`|
|标识|`__DOS__`|

### Symbian OS
|类型|宏|
|-|-|
|标识|`__SYMBIAN32__`|

### UNIX Environment
|类型|宏|
|-|-|
|标识|`__unix__`|
|标识|`__unix`|

### Windows
|类型|宏|描述|
|-|-|-|
|标识|`_WIN16`|Defined for 16-bit environments [~](https://docs.microsoft.com/en-us/windows-hardware/drivers/kernel/64-bit-compiler)|
|标识|`_WIN32`|Defined for both 32-bit and 64-bit environments [~](https://docs.microsoft.com/en-us/windows-hardware/drivers/kernel/64-bit-compiler)|
|标识|`_WIN64`|Defined for 64-bit environments [~](https://docs.microsoft.com/en-us/windows-hardware/drivers/kernel/64-bit-compiler)|
|标识|`__WIN32__`|Defined by Borland C++|
|标识|`__TOS_WIN__`|Defined by xlC|
|标识|`__WINDOWS__`|Defined by Watcom C/C++|

### Windows CE
|类型|宏|格式|描述|
|-|-|-|-|
|标识|`_WIN32_WCE`||Defined by Embedded Visual Studio C++|
|版本|`_WIN32_WCE`|VRR|V = Version, R = Revision|
|标识|`WIN32_PLATFORM_'P'`||P = Platform|
|版本|`WIN32_PLATFORM_'P'`|V|P = Platform, V = Version|

例子  
|版本|`_WIN32_WCE`|
|-|-|
|2.01|201|
|2.11|211|
|3.0|300|
|4.0|400|
|4.1|410|
|4.2|420|
|5.0|501|

## C/C++编译器预定义宏

### Borland C++
|类型|宏|格式|
|-|-|-|
|标识|`__BORLANDC__`||
|版本|`__BORLANDC__`||
|标识|`__CODEGEARC__`||
|版本|`__CODEGEARC__`|From C++ Builder 2006|

例子  
|Borland C++|C++ Builder|`__BORLANDC__`|`__CODEGEARC__`|
|-|-|-|-|
|2.0||0x200||
|3.0||0x400||
|3.1||0x410||
|4.0||0x452||
|5.0||0x500||
|5.0.2|1.0|0x520||
||3.0|0x530||
||4.0|0x540||
|5.5|5.0|0x550||
|5.5.1||0x551||
|5.6.4||0x562||
||2006|0x570|0x570|
||2007|0x590|0x590|
||2009|0x613|0x613|
||2010|0x621|0x621|
||XE|0x630|0x630|
||XE2|0x640|0x640|
||XE3|0x650|0x650|
||XE4|0x660|0x660|

### CLang
|类型|宏|描述|
|-|-|-|
|标识|`__clang__`||
|版本|`__clang_major__`|Version|
|版本|`__clang_minor__`|Revision|
|版本|`__clang_patchlevel__`|Patch|

### GCC C/C++
|类型|宏|描述|
|-|-|-|
|标识|`__GNUC__`||
|版本|`__GNUC__`|Version|
|版本|`__GNUC_MINOR__`|Revision|
|版本|`__GNUC_PATCHLEVEL__`|Patch (introduced in version 3.0)|

Notice that the meaning of the __GNUC__ macro has changed subtly over the years, from identifying the GNU C/C++ compiler to identifying any compiler that implements the GNU compiler extensions (see the [Feature request - a macro defined for GCC](https://gcc.gnu.org/ml/gcc/2008-07/threads.html#00025) discussion for further information). For example, the Intel C++ on Linux also defines these macros from version 8.1 (see the [Intel C++ Compiler 8.1 for Linux Release Notes](ftp://download.intel.com/support/performancetools/c/linux/sb/clin81_relnotes.pdf) and Intel Compilers for [Linux: Compatibility with GNU Compilers](https://software.intel.com/en-us/articles/intel-compilers-for-linux-compatibility-with-gnu-compilers).)

例子  
|GNU C/C++|`__GNUC__`|`__GNUC_MINOR__`|`__GNUC_PATCHLEVEL__`|
|-|-|-|-|
|2.7.x|2|7|N/A|
|3.0.2|3|0|2|

If you prefer a single version macro, you can define the following yourself.
```C++
#if defined(__GNUC__)
# if defined(__GNUC_PATCHLEVEL__)
#  define __GNUC_VERSION__ (__GNUC__ * 10000 \
                            + __GNUC_MINOR__ * 100 \
                            + __GNUC_PATCHLEVEL__)
# else
#  define __GNUC_VERSION__ (__GNUC__ * 10000 \
                            + __GNUC_MINOR__ * 100)
# endif
#endif
```
The format of this new macro is:
|类型|宏|格式|描述|
|-|-|-|-|
|Version|`__GNUC_VERSION__`|VVRRPP|VV = Version,RR = Revision,PP = Patch|

例子  
|GNU C/C++|`__GNUC_VERSION__`|  
|-|-|
|2.7.x|20700|  
|3.0.2|30002|  

### Microsoft Visual C++
|Type|Macro|Format|Description|
|-|-|-|-|
|Identification|`_MSC_VER`|||	
|Version|`_MSC_VER`|VVRR|VV = Version,RR = Revision|
|Version|`_MSC_FULL_VER`|VVRRPPPP|VV = Version,RR = Revision,PPPP = Patch,From Visual C++ 6.0 Processor Pack|
|Version|`_MSC_FULL_VER`|VVRRPPPPP|VV = Version,RR = Revision,PPPPP = Patch,From Visual C++ 8.0|
|Version|`_MSC_BUILD`|B|B = Build number,From Visual C++ 9.0|

例子  
|Visual C++ [~](https://docs.microsoft.com/en-us/previous-versions/b0084kay(v=vs.140))  [~](https://social.msdn.microsoft.com/Forums/vstudio/en-US/c3f1ba1f-c59d-46a3-a028-bcfabe9fbe2c/how-do-you-check-which-compiler-you-are-using-in-c?forum=vcgeneral)|`_MSC_VER`|`_MSC_FULL_VER`|
|-|-|-|
|1.0|800||
|3.0|900||
|4.0|1000||
|4.2|1020||
|5.0|1100||
|6.0|1200||
|6.0 SP6|1200|12008804|
|7.0|1300|13009466|
|7.1 (2003)|1310|13103077|
|8.0 (2005)|1400|140050727|
|9.0 (2008)|1500|150021022|
|9.0 SP1|1500|150030729|
|10.0 (2010)|1600|160030319|
|10.0 (2010) SP1|1600|160040219|
|11.0 (2012)|1700|170050727|
|12.0 (2013)|1800|180021005|
|14.0 (2015)|1900|190023026|
|14.0 (2015 Update 1)|1900|190023506|
|14.0 (2015 Update 2)|1900|190023918|
|14.0 (2015 Update 3)|1900|190024210|
|15.0 (2017)|1910|191025017|

### MinGW and MinGW-w64
MinGW (formerly known as MinGW32) is a toolchain for creating 32 Bit Windows executables. The MinGW-w64 projects offers toolchains for creating 32 Bit and 64 Bit Windows executables. The following table shows which macros are defined by each toolchain:  
|Type|Macro|Description|MinGW32|MinGW-w64 32 Bit|MinGW-w64 64 Bit|
|-|-|-|-|-|-|
|Identification|`__MINGW32__`|defined|defined|defined|
|Version|`__MINGW32_MAJOR_VERSION`|Version|defined|defined|defined|
|Version|`__MINGW32_MINOR_VERSION`|Revision|defined|defined|defined|
|Identification|`__MINGW64__`|-|-|defined|
|Version|`__MINGW64_VERSION_MAJOR`|Version|-|defined|defined|
|Version|`__MINGW64_VERSION_MINOR`|Revision|-|defined|defined|
Notice that `__MINGW32_MAJOR_VERSION`, `__MINGW32_MINOR_VERSION`, `__MINGW64_VERSION_MAJOR`, and `__MINGW64_VERSION_MINOR`
are only defined if appropriate headers are included. Appropriate headers are
<stdlib.h>, <stdio.h>, <windows.h>, <windef.h>, and probably more.

例子   
|`__MINGW32_MAJOR_VERSION`|`__MINGW32_MINOR_VERSION`|`__MINGW64_VERSION_MAJOR`|`__MINGW64_VERSION_MINOR`|Description|
|-|-|-|-|-|
|2|4 |||MinGW32 2.4|
|3|20|||MinGW32 3.20|
|3|11|2|0|MinGW-w64|2.0|



**Reference**:  
[https://sourceforge.net/p/predef/wiki/OperatingSystems/](https://sourceforge.net/p/predef/wiki/OperatingSystems/)  
[https://sourceforge.net/p/predef/wiki/Compilers/](https://sourceforge.net/p/predef/wiki/Compilers/)
