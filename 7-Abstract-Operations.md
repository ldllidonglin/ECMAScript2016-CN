## 第七章：抽象操作

这些操作不是ECMAScript语言的一部分。他们单独的在这里定义是为了帮助理解本规范，其他更多专业的抽象操作在本规范中定义。

### 7.1 类型转换

ECMAScript语言会在需要的时候发生隐式转换，为了让某些操作概念更加清晰，所以定义了一组操作操作，转换抽象操作有多种形态，他们可以接受任何ECMAScript语言类型值，但是不接受规范类型值

#### 7.1.1 ToPrimitive\( input \[ , PreferredType \] \)

这个抽象操作接受一个input参数以及一个可选的PreferredType参数，它会把input转换为非对象类型，如果是一个对象可以被转换成多种原始类型，这时就可以使用PreferredType参数来规定转换为哪种类型，转换方式参照表9

##### 表9

| Input类型 | 结果 |
| --- | --- |
| Undefined | 返回input |
| Null | 返回input |
| Boolean | 返回input |
| Number | 返回input |
| String | 返回input |
| Symbol | 返回input |
| Ojbect | 运用下面的步骤 |

当Type\(input\)是对象类型时，会运用以下步骤：

1. 如果没有PreferredType参数，设hint值为“default”。
2. 否则如果PreferredType参数是String，设hint值为“string”。
3. 否则PreferredType参数是Number，设hint值是“number”。
4. 让 exoticToPrim 是GetMethod\(input, @@toPrimitive\)的值。
5. 如果 exoticToPrim 不是undefined，就：
   1. 设result值是 Call\(exoticToPrim, input, « hint »\)
   2. 如果 Type\(result\) 不是对象类型, 返回result.
   3. 抛出TypeError异常
6. 如果hint是“default”，设hint是“number”
7. 返回OrdinaryToPrimitive\(input, hint\)

当抽象操作OrdinaryToPrimitive 以参数o和hint调用时，运用以下步骤：

1. 断言：Type\(o\)是Object类型
2. 断言：Type\(hint\)是String并且它的值是“string”，或者“number”.
3. 如果hint是“string”，就:
   1. 让methodNames是« "toString", "valueOf" » 
4. 否则
   1. 让methodNames是« "valueOf", "toString" ».
5. 循环methodNames列表中的函数名name：
   1. 让method是Get\(O, name\)
   2. 如果 IsCallable\(method\) 值为true，就：
      1. 让result是Call\(method, O\)的返回值
      2. 如果Type\(result\)不是Object,返回result
6. 抛出TypeError异常

> 注意：当ToPrimitive不带hint参数调用时，它通常会被被当作hint是Number一样执行，然而对象类型可以通过顶一个一个@@toPrimitive函数重写这个操作。本规范中定义的Object类型只有Date对象和Symbol对象重写了默认的ToPrimitive行为，Date对象如果hint是String，会被当作没有hint处理

#### 7.1.2 ToBoolean\( argument \)

这个抽象操作的的转换方式惨遭表10

##### 表10：

| argument类型 | 结果 |
| --- | --- |
| Undefined | 返回false |
| Null | 返回false |
| Boolean | 返回argument |
| Number | 返回false,如果argument是+0，-0，NaN，其他情况一律返回true |
| String | 返回falser,如果argument是一个空字符串（length是0）,其他情况一律返回true |
| Symbol | 返回true |
| Ojbect | 返回true |

#### 7.1.3 ToNumber\( argument \)

这个抽象操作的的转换方式惨遭表11

##### 表11：

| argument类型 | 结果 |
| --- | --- |
| Undefined | 返回NaN |
| Null | 返回+0 |
| Boolean | argument是true，返回1，argument是false返回+0 |
| Number | 返回argument |
| String | 参照下文的转换算法步骤 |
| Symbol | 抛出TypeError异常 |
| Ojbect | 运用以下步骤：1. 设primValue是ToPrimitive\(argument,hint Number\).2. 返回ToNumber\(primValue\) |

##### 7.1.3.1 ToNumber运用于String类型

对字符串应用ToNumber时，对输入字符串应用如下文法，如果此文法还是无法把字符串解释为_字符串数值常量_，结果将会是NaN。

> 注意1:这个语法中的符号都是由Unicode编码组成，如果String包含UTF-16编码之外补充的编码形式或者任何不接受的编码形式，都将返回NaN

语法：

```
StringNumericLiteral:::
  StrWhiteSpaceopt
  StrWhiteSpaceoptStrNumericLiteralStrWhiteSpaceopt
StrWhiteSpace:::
  StrWhiteSpaceCharStrWhiteSpaceopt
StrWhiteSpaceChar:::
  WhiteSpace
  LineTerminator
StrNumericLiteral:::
  StrDecimalLiteral
  BinaryIntegerLiteral
  OctalIntegerLiteral
  HexIntegerLiteral
StrDecimalLiteral:::
  StrUnsignedDecimalLiteral
  +StrUnsignedDecimalLiteral
  -StrUnsignedDecimalLiteral
StrUnsignedDecimalLiteral:::
  Infinity
  DecimalDigits.DecimalDigitsoptExponentPartopt
  .DecimalDigitsExponentPartopt
  DecimalDigitsExponentPartopt
```

以上所有没有明确定义的词法符号都在第十一章词法文法中中定义了

> 注意2：注意字符串数值常量和数值常量有一些不同：
>
> * 字符串数值常量中的 / 符号之前或者之后可能会有空白和行终结符
> * 十进制的字符串数值常量可能会有多个0在前面
> * 十进制的字符串数值常量可能会有+ 或者 - 符号前缀
> * 字符串数值常量空或者只包含空白符会转换成 +0
> * Infinity和-infinity被认为是字符串数值常量，不是数值常量

###### 7.1.3.1.1 运行过程

总的来说，把String转换为Number和确定一个数字字符串的数值类似，但是在细节上还是有一些区别，所以整个处理过程在这里给出。确定其数值有两步：首先，一个数值是从字符串数值来的，第二，以下面所描述的方式对数值进行舍入。任何下面没有提供语法的都在11.8.3.1中提供了。

* \[empty\] 是0
* 空白符是0
* "空白符_数值常量_空白符" 是数值常量的数值
* 二进制、八进制、十进制、十六进制数字字符串是 这个字符串的数值
* 无符号整型 是无符号整型的数学值
* “ + 无符号整型”是无符号整型的数学值
* “ - 无符号整型”是无符号整型的数学值的负数（注意，如果无符号整型是0，那它的负数也是0，下面的规则会描述把无符号数字0转换为+0或者-0的舍入规则）
* Infinity是10^10000（值太大，舍入为  +∞）
* “十进制数 . ” 的数学值是十进制数
* “十进制数 . 十进制数” 的数学值是第一个十进制数加第二个十进制数\*10^-n，其中n是第二个十进制数的数字个数
* “十进制数 . 指数部分” 的数学值是十进制数的数学值\*10^e，其中e是指指数部分的数学值
* “十进制数 . 十进制数 指数部分” 的数学值是第一个十进制数加（第二个十进制数\*10^-n）,然后再乘以10^e，其中其中n是第二个十进制数的数字个数，e是指指数部分的数学值
* “ . 十进制数” 是这个十进制数乘以10^-n，其中n是第二个十进制数的数字个数
* “ . 十进制数 指数部分” 数学值是十进制数乘以10^e-n，其中n是第二个十进制数的数字个数，e是指指数部分的数学值
* 十进制数 的数字值是这个十进制数的数字值
* “十进制数 指数部分” 数学值是十进制数乘以10^e，其中e是指数部分

一旦一个字符串数值常量的数学值被确定了，那就会被舍入为一个Number类型值。如果其数学值是0，那舍入的值为 +0 ，除非字符串数数值常量前第一个非空白符是“-”，这时就会被舍入为-0。否则舍入值必须是如6.1.6中规定的这个数学值的Number类型值，除非这个字符串包含无符号十进制数并且有效数字个数大于20，在这个情况下，要么是把20位以后的值用0替换，或者20位以后的值用0替换，并且在第20位数字加1。一个数字是否是有效数字判断条件就是首先不是指数部分，并且不是0或者它左边有一个非零数字，右边有一个非零或者非指数部分的数字

#### 7.1.4 ToInteger \( argument \)

ToInteger这个抽象操作把argument转换为一个整数，抽象操作的操作过程如下：

1. 让 number 是ToNumber\(argument\)的值
2. 如果 number 是NaN，返回 +0
3. 如果是 +0，-0，+∞， -∞,返回number
4. 返回和number同样符号，并且大小是floor\(abs\(number\)\)

#### 7.1.5 ToInt32 \( argument \)

ToInt32这个抽象操作把argument转换为-2^31到2^31-1这个闭区间范围内的共2^32个整数，这个抽象操作的操作过程如下：

1. 让number是ToNumber\(argument\)
2. 如果number是NaN,+0,-0,+∞,-∞,染回+0
3. 让int是和number同样符号，大小是floor\(abs\(number\)\)
4. 让int32bit是int modulo 2^32
5. 如果int32bit &gt;= 2^31，返回int32bit - 2^32；否则返回int32bit

> 注意：鉴于上述ToInt32的定义：
>
> * ToInt32这个抽象操作是幂等的：如果通过步骤得到一个值，那同样操作再执行一遍，得到的结果是不变的
> * 对于任意的x值，ToInt32\(ToInt32\(x\)\)得到的值和ToInt32\(x\)得到的值是一样的（It is to preserve this latter property that +∞ and -∞ are mapped to +0.\)。
> * ToInt32把-0映射为+0

#### 7.1.6 ToUint32 \( argument \)

ToUnit32这个抽象操作把argument转换为 0 到 2^32-1 这个闭区间范围内的共2^32个整数。这个抽象操作的操作过程如下：  
1. 让number是ToNumber\(argument\)  
2. 如果number是NaN,+0,-0,+∞,-∞,染回+0  
3. 让 int 是和number同样符号，大小是floor\(abs\(number\)\)  
4. 让 int32bit 是int modulo 2^32  
5. 返回 int32bit

> 注意：鉴于上述ToUint32的定义：
>
> * 第五步是和ToInt32的唯一区别
> * ToUint32这个抽象操作是幂等的：如果通过步骤得到一个值，那同样操作再执行一遍，得到的结果是不变的
> * 对于任意的x值，ToUint32\(ToUint32\(x\)\)得到的值和ToUint32\(x\)得到的值是一样的（It is to preserve this latter property that +∞ and -∞ are mapped to +0.\)。
> * ToUint32把-0映射为+0

#### 7.1.7 ToInt16 \( argument \)

ToInt16 这个抽象操作把argument转换为 -32767 到 32767 这个闭区间范围内的共2^16个整数。这个抽象操作的操作过程如下：

1. 使 number 是ToNumber\(argument\)
2. 如果 number 是NaN,+0,-0,+∞,-∞,染回+0
3. 使 int 是和 number 同样符号，大小是floor\(abs\(number\)\)
4. 使 int16bit 是int modulo 2^16
5. 如果int16bit &gt;= 2^15，返回int16bit - 2^16；否则返回int16bit

#### 7.1.8 ToUint16 \( argument \)

ToUint16 这个抽象操作把argument转换为 0 到 2^16-1 这个闭区间范围内的共2^16个整数。这个抽象操作的操作过程如下：

1. 使 number 是ToNumber\(argument\)
2. 如果 number 是NaN,+0,-0,+∞,-∞,染回+0
3. 使 int 是和 number 同样符号，大小是floor\(abs\(number\)\)
4. 使 int16bit 是int modulo 2^16
5. 返回int16bit

> 注意：鉴于上述给出的ToUint16定义：
>
> * 第四步中的把2^32换成2^16是ToUint32和ToUint16唯一的区别
> * ToUint16映射 -0 为 +0

#### 7.1.9 ToInt8 \( argument \)

ToInt8 这个抽象操作把argument转换为 -128 到 127 这个闭区间范围内的共2^8个整数。这个抽象操作的操作过程如下：

1. 使 number 是ToNumber\(argument\)
2. 如果 number 是NaN,+0,-0,+∞,-∞,染回+0
3. 使 int 是和 number 同样符号，大小是floor\(abs\(number\)\)
4. 使 int8bit 是int modulo 2^8
5. 如果int8bit &gt;= 2^7，返回int16bit - 2^8；否则返回int8bit

#### 7.1.10 ToUint8 \( argument \)

ToUint8 这个抽象操作把argument转换为 0 到 255 这个闭区间范围内的共2^8个整数。这个抽象操作的操作过程如下：

1. 使 number 是ToNumber\(argument\)
2. 如果 number 是NaN,+0,-0,+∞,-∞,染回+0
3. 使 int 是和 number 同样符号，大小是floor\(abs\(number\)\)
4. 使 int8bit 是int modulo 2^8
5. 返回int8bit

#### 7.1.11 ToUint8Clamp \( argument \)

ToUint8 这个抽象操作把argument转换为 0 到 255 这个闭区间范围内的共2^8个整数。这个抽象操作的操作过程如下：

1. 使 number 是ToNumber\(argument\)
2. 如果 number 是NaN，返回 +0
3. 如果 number &lt;= 0，返回 +0
4. 如果 number &gt;= 255，返回 255
5. 使 f 是floor\(number\)
6. 如果 f + 0.5 &lt; number，那么返回 f + 1
7. 如果 number &lt; f + 0.5，返回 f
8. 如果 f 是奇数，返回 f + 1
9. 返回 f

> 注意：不像ECMASCript中其他的转换抽象操作一样，ToUnit8Clamp的舍入不仅仅是缩短非整数并且没有把  +∞ 转为0.ToUint8Clamp是舍入为偶数，这是和Math.round四舍五入法所不同的地方

#### 7.1.12 ToString \( argument \)

ToString这个抽象操作根据表12把argument转换为String类型的值

##### 表12

| argument类型 | 结果 |
| --- | --- |
| Undefined | 返回"undefined" |
| Null | 返回"null" |
| Boolean | argument是true，返回"true"，argument是false返回"false" |
| Number | 参见7.1.12.1 |
| String | 返回argument |
| Symbol | 抛出TypeError异常 |
| Ojbect | 运用以下步骤：1. 设primValue是ToPrimitive\(argument,hint Number\).2. 返回ToString\(primValue\) |

##### 7.1.12.1 针对Number类型的ToString操作

针对Number类型值 m 的ToString操作过程如下：

1. 如果 m 是NaN,返回字符串 "NaN"
2. 如果 m 是 +0 或者 -0，返回字符串 "0"
3. 如果 m 小于0，返回字符串 "-" 和 ToString\(-m\)
4. 如果 m 是 +∞，返回字符串 "Infinity"
5. 否则使 n, k, s是整数，并且满足 k &gt;= 1,10^k-1 &lt;= s &lt; 10^k，m=s\*10^n-k，而且 k 尽可能的小。注意 k 是 s 十进制表示时的数字个数，s 不能被10整除，并且s的最少有效数字个数不是由这些标准唯一确定。
6. 如果 k &lt;= n &lt;= 21，返回由 k 个 s 的十进制表示的数字组成的字符串（有序的，并且最前面没有0），后面是 n-k 个字符\(数字0\)
7. 如果 0 &lt; n &lt;=21，最多返回 n 个 s 的十进制表示的有效数字组成的字符串，后面跟着小数点，再后面是剩下的 k - n 个 s 十进制表示的有效数字。
8. 如果-6 &lt; n &lt;= 0，返回“0“字符串，后面是小数点，再后面是 -n 个0，再后面是 k 个 s 的十进制表示的有效数字。
9. 否则，如果 k 等于 1，返回由s十进制表示的数字组成的字符串，然后后面是指数e，然后是 + 或者 - ，这取决于 n-1是正数还是负数，再然后是abs\(n-1\)的十进制表示数（前面没有0）
10. 返回 s 的十进制表示的数字组成的字符串，后面是小数点，然后是 剩下的k-1 个十进制表示的s的数字，然后是e，然后是+ 或者 - ，这取决于 n-1是正数还是负数，再然后是abs\(n-1\)的十进制表示数（前面没有0）

> 注意1 下面的评价不是本规范的内容，但是可能是有用的：
>
> * 任意 非 -0 的Number值x，ToNumber\(ToString\(x\)\)的值和x是一样的
> * s 的最少有效数字个数并不是由第五步唯一确定的
>
> 注意2 如果要实现比上面规定的步骤得到结果更精确的转换，推荐下面的第五步实现：  
> 5. 否则，使 n, k, s是整数，并且满足 k &gt;= 1,10^k-1 &lt;= s &lt; 10^k，m=s\*10^n-k，而且 k 尽可能的小。如果 s 有多种情况，选择更接近 m 的那个 s ，如果 s 有两种可能，选择偶数注意 k 是 s 十进制表示时的数字个数，s 不能被10整除。
>
> 注意3 ECMAScript的实现者可能会发现，David.M所写的关于浮点数进行二进制到十进制转换方面的文章和代码很有帮助：
>
> David.M所写的准确的二进制和十进制的转换，数值分析，手稿90-10，贝尔实验室，1990.11.30可以在[http://ampl.com/REFS/abstracts.html\#rounding](http://ampl.com/REFS/abstracts.html#rounding)找到，代码在[http://netlib.sandia.gov/fp/dtoa.c](http://netlib.sandia.gov/fp/dtoa.c)或者[http://netlib.sandia.gov/fp/g\_fmt.c](http://netlib.sandia.gov/fp/g_fmt.c)，或者在其他很多镜像站也能找到

#### 7.1.13 ToObject（argument）
ToObject这个抽象操作参照表13把argument转换为Object类型

##### 表13

| argument类型 | 结果 |
| --- | --- |
| Undefined | 抛出TypeError异常 |
| Null | 抛出TypeError异常|
| Boolean | 返回一个新的 Boolean 对象，其[[BooleanData]]内部值是argument，参见19.3关于 Boolean 对象的描述 |
| Number | 返回一个新的 Number 对象，其[[NumberData]]内部值是argument，参见19.3关于 Number 对象的描述 |
| String | 返回一个新的 String 对象，其[[StringData]]内部值是argument，参见19.3关于 String 对象的描述 |
| Symbol | 返回一个新的 Symbol 对象，其[[SymbolData]]内部值是argument，参见19.3关于 Symbol 对象的描述 |
| Ojbect | 返回argument |

#### 7.1.14 ToProperty ( argument )
ToProperty这个抽象操作把argument转换为一个能当作属性名的值，其操作过程如下：

1. 使key是ToPrimitive（argument，hint String）
2. 如果Type(key) 是Symbol，返回key
3. 返回ToString(key)

#### 7.1.15 ToLength( argument )
ToLength这个抽象操作把argument转换为一个能用作类数组对象的length属性的整数值，其操作过程如下：
1. 使len是ToInteger(argument)
2. 如果len <= +0,返回 +0
3. 如果len是+∞，返回2^53-1
4. 返回 min(len,2^53-1)

#### 7.1.16 CanonicalNumericIndexString ( argument )
CanonicalNumericIndexString这个抽象操作把argument转换为一个数字值，如果argument是一个由ToString产生的数字字符串或者"-0",否则返回undefined，其操作过程如下：

1. 断言：Type（argument)是String
2. 如果argument是"-0"，返回-0
3. 使n是ToNumber(argument）的值
4. 如果SameValue(ToString(n),argument)是false，返回undefined
5. 返回n

一个 canonical numeric string 是指任意一个经CanonicalNumericIndexString 抽象操作不返回undefined的String值