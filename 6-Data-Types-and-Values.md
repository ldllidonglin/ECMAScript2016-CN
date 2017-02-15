# 第六章 ECMAScript Data Types and Values
在本规范中的算法所操作的各种值都有相对应的类型，本章就是定义这些值的类型。类型又再分为ECMAScript语言类型和规范类型。  
在本规范中，“Type（x）”是“the type of x”的简写，其中的“type”就是在本章中定义的ECMAScript语言类型和规范类型。如果是没有任何类型的值，就用"empty"来指代

## 6.1 ECMAScript语言类型
ECMAScript语言类型就是指可以直接在由ECMAScript编写的程序中操作使用的值的类型，有以下几种：Undefined,Null,Boolean,String,Symbol,Number,Object。ECMAScript语言值有一一对应的ECMAScript语言类型
### 6.1.1 Undefined类型

Undefined类型只有一种值，就是undefined，任何变量在没有被赋值之前都有undefined值

### 6.1.2 Null类型

Null类型只有一种值，就是null

### 6.1.3 Boolean类型

Boolean 类型表示逻辑实体，有两个值，true 和 false。

### 6.1.4 String类型
The String type is the set of all ordered sequences of zero or more 16-bit unsigned integer values \(“elements”\) up to a maximum length of 253-1 elements.字符串类型通常被用于表示文本数据，这时String当中的每一个元素都被当作UTF-16编码的值。每个元素被当作一个在这个序列中的占位符。序列中的位置索引都是非负数。第一个元素的索引值是0，第二个元素是1，按此排列。String的length值就是其元素的个数，空字符串的length是0，所以它不包含任何元素。

在ECMAScript中解释运行String值时，其每一个元素都被当作UTF-16编码，然而，ECMAScript没有对String当中的元素作任何编码限制，所以他们可能被错误的解释运行。Operations that do not interpret String contents treat them as sequences of undifferentiated 16-bit unsigned integers。String.prototype.normalize（21.1.3.12）函数能按照指定的规则正规化String值。String.prototype.localeCompare（21.1.3.10）会在函数内把String值正规化。没有其他操作会隐式的正规化String值。只有显示指定规则或者本地语言规定才会发生这样的操作。

> 注意：这种设计实现的背后原理就是为了能让String的实现即简单又能尽可能的高性能。如果ECMAScript源代码被正规化像C语言那样，String也会被当作是已经正规化了的，只要他们不包含任何Unicode转义序列。
一些操作会把String内容当作UTF-16编码的Unicode代码点，在这种情况下，操作流程如下：


* 一个字符单元如果是在0到0xD7FF或者0xE000到0xFFFF范围内，会被解释为同样的值
* 一个两个字符的序列，当第一个字符c1在0xD800到0xDBFF范围中，并且第二个字符c2在0xDC00到0xDFFF中，是一个代理对，并且会被解释执行一个值为（c1-0xD800）× 0x400 + \(c2 - 0xDC00\) + 0x10000的代码代码单元（10.1.2）
* 一个字符单元在0xD800到0xDFFF范围中是，但是不是一个代理对，会被解释执行为同样的值  

### 6.1.5 Symbol类型

Symbol类型是一组非String值的集合，可以被当作Object属性的key值  
任意一个Symbol值都是独一无二并且是不可变的  
任意一个Symbol值都持有一个名叫\[\[Description\]\]的关联值，这个值要么是undefined，要么是String值

#### 6.1.5.1 Symbol特性

Symbol特性是Symbol值根据本规范算法规定的内置属性值，他们一般被当作规范扩展的那些点中属性的key值，除非另外规定，symbol特性都共享在8.2中。symbol特性都用@@name的形式标记，name值主要有如下表1如实

| 特性名 | 描述 | V值和作用 |
| --- | --- | --- |
| @@hasInstance | "Symbol.hasInstance" | 一个方法用来识别构造函数对象是否是构造函数的实例，在instance of 操作时调用 |
| @@isConcatSpreadable | "Symbol.isConcatSpreadable" | 一个布尔值，当为true时意味着它的array元素可以被Array.prototype.concat方法打平 |
| @@iterator | "Symbol.iterator" | 一个方法，给一个对象返回默认的迭代器，当用for-of时调用 |
| @@match | "Symbol.match" | 一个正则表达式方法，用来给字符串匹配正则表达式，当用String.prototype.match方法时调用 |
| @@replace | "Symbol.replace" | 一个正则表达式方法，用来替换匹配了的子字符串， 当用String.prototype.search 方法时调用 |
| @@search | "Symbol.search" | 一个正则表达式，返回正则表达式比配上的字符串的索引值，当用 String.prototype.search方法时调用 |
| @@species | "Symbol.species" | 用来构建派生对象的构造函数的函数值属性 |
| @@split | "Symbol.split" | 一个正则表达式方法，用来根据匹配上正则表达式的索引分割字符串 |
| @@toPrimitive | "Symbol.toPrimitive" | 一个用来把对象转换为原始值的函数，当用ToPrimitive时调用 |
| @@toStringTag | "Symbol.toStringTag" | 一个String值，用来创建默认String对象的描述，当用内置方法Object.prototype.toString方法时可访问 |
| @@unscopables | "Symbol.unscopables" | An object valued property whose own and inherited property names are property names that are excluded from the with environment bindings of the associated object |

### 6.1.6 Number类型

   准确的说，数值类型拥有 18437736874454810627（即2^64-2^53 +3）个值，表示为IEEE 754-2008标准64位双精度数值，在IEEE二进制浮点数算法中定义的，减去IEEE标准中的 9007199254740990（即2^53-2）个明显的“非数字”值，在 ECMAScript 中，它们被表示为一个单独的特殊值：NaN。（请注意，NaN值由程序表达式 NaN 产生，并假设执行程序不能调整定义的全局变量 NaN）。在某些实现中，外部代码可以识别出非数字值之间的不同，但是这依赖于具体实现，所有的NaN值都是无法互相区分的。

> 注意：在ArrayBuffer（24.1）的位模式中可能会发现存储的一个数字值不一定会相等于在ECMAScript中使用的值
    有两个特殊值，正无穷大和负无穷大，为简洁起见，这些值有时会被用+∞和 -∞表示（这两个无穷大值可以用+Infinity（或者Infinityh）和-Infinity产生\)  

其他18437736874454810627（即2^64-2^53 +3）个值称为有限值，一半是正值，一半是负值。对于每个正数而言，都有一个与之对应的、相同大小的负数。  
注意这里也有一个正零和负零，同样的为了简洁，会用+0和-0表示（注意这两个值也由+0\(0\)或者-0产生）  
这18437736874454810622 （2^64-2^53-2）个有限非零值分为两种：  
18428729675200069632 \(2^64-2^54\)个是格式化的，有如下格式:

```
s × m × 2^e
```

这里的s是+1或者-1，m是正整数，小于2^53大于2^52，e是一个从-1074到971的整数

剩下的9007199254740990（2^53-2）个值是非常规的，有如下格式：

```
s × m × 2^e
```

这里的s是+1或者-1，m是正整数，小于2^52，e是-1074

注意，所有绝对值不大于2^53的无论是正数还是负数都可以用Number类型来表示（甚至，0有两种表示方法，+0和-0）

一个有限的非零数值用上述的两种格式表示时，m是偶数，那他就有一个偶数标记，否则就有一个奇数标记

本规范中，当x表示一个精确的非零数值（甚至可以是无理数，比如π），"the Number value for x"这句话的意思就是以以下的方式选择一个数值：从所有有限数值集合中（不包括-0，包括2^1024和-2^2014这两个没法表示的值）选择一个最接近x的值。如果有两个是最接近x的值，那就选择有偶数标记的。2^1024和-2^1024都是当作有偶数标识的。最终，如果2^1024是最接近的那个值，用+∞代替，如果是-2^1024，那就用-∞代替，如果是+0，只有x&lt;0时，才会用-0代替。其他选择的值不会发生改变（这个过程是在符合IEEE 754-2008中的“round to nearest, ties to even”模式）。

一些ECMAScript操作符只会处理在-2^31到2^31 - 1 范围内的数值，或者0到2^16-1。这些操作符接受任何类型的Number类型，但是会先把操作数转换到所只会处理的范围，转换操作详见7.1

### 6.1.7 Object类型



