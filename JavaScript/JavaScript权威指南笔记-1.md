# 概述
JavaScript是一门高端、动态、弱类型编程语言，适合面向对象和函数式编程风格。  

# 词法结构
- JavaScript程序用Unicode字符集编写。  
- JavaScrpt是大小写敏感的语言。
- JavaScript会忽略程序中标识(token)间的空格。
- JavaScript使用6个ASCII表示任意16位Unicode内码，称为Unicode转义序列，以\u为前缀，其后跟随4个十六进制数
- JavaScript使用分号分隔语句，如果语句单独成一行，分号也可以省略。
- JavaScript在缺少分号后无法解析的行末添加分号。
```JavaScript 
var a
a
=
3
console.log(a)

//解析为var a;a=3;console.log(a);
```
- return、 break、continue语句不能换行，否则其后会添加分号。
```JavaScript
return 
true

//解析为return; true;
```
- ++、--作为后缀运算符不能分行，否则会作为后面标识符的前缀运算符。
```JavaScript
x
++
y
//解析为x;++y;
```

# 类型、值和变量
- JavaScript的数据类型分为：原始类型(primitive type)和对象类型(object type)。
- 原始类型包括：数字、字符串、布尔值。