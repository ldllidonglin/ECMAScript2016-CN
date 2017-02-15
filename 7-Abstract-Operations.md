## 第七章：抽象操作
这些操作不是ECMAScript语言的一部分。他们单独的在这里定义是为了帮助理解本规范，其他更多专业的抽象操作在本规范中定义。
### 7.1 类型转换
ECMAScript语言会在需要的时候发生隐式转换，为了让某些操作概念更加清晰，所以定义了一组操作操作，转换抽象操作有多种形态，他们可以接受任何ECMAScript语言类型值，但是不接受规范类型值
#### 7.1.1 ToPrimitive( input [ , PreferredType ] )
这个抽象操作接受一个input参数以及一个可选的PreferredType参数，它会把input转换为非对象类型，如果是一个对象可以被转换成多种原始类型，这时就可以使用PreferredType参数来规定转换为哪种类型，转换方式参照表9
##### 表9
|Input类型|结果|
|----|---------|
|Undefined|返回input|
|Null|返回input|
|Boolean|返回input|
|Number|返回input|
|String|返回input|
|Symbol|返回input|
|Ojbect|运用下面的步骤|

当Type(input)是对象类型时，会运用以下步骤：

1. 如果没有PreferredType参数，设hint值为“default”。
2. 否则如果PreferredType参数是String，设hint值为“string”。
3. 否则PreferredType参数是Number，设hint值是“number”。
4. 让 exoticToPrim 是GetMethod(input, @@toPrimitive)的值。
5. 如果 exoticToPrim 不是undefined，就：
  1. 设result值是 Call(exoticToPrim, input, « hint »)
  2. 如果 Type(result) 不是对象类型, 返回result.
  3. 抛出TypeError异常
6. 如果hint是“default”，设hint是“number”
7. 返回OrdinaryToPrimitive(input, hint)

当抽象操作OrdinaryToPrimitive 以参数o和hint调用时，运用以下步骤：

1. 断言：Type(o)是Object类型
2. 断言：Type(hint)是String并且它的值是“string”，或者“number”.
3. 如果hint是“string”，就:
  1. 让methodNames是« "toString", "valueOf" » 
4. 否则
   1. 让methodNames是« "valueOf", "toString" ».
5. 循环methodNames列表中的函数名name：
  1. 让method是Get(O, name)
  2. 如果 IsCallable(method) 值为true，就：
    1. 让result是Call(method, O)的返回值
    2. 如果Type(result)不是Object,返回result
6. 抛出TypeError异常

> 注意：当ToPrimitive不带hint参数调用时，它通常会被被当作hint是Number一样执行，然而对象类型可以通过顶一个一个@@toPrimitive函数重写这个操作。本规范中定义的Object类型只有Date对象和Symbol对象重写了默认的ToPrimitive行为，Date对象如果hint是String，会被当作没有hint处理

#### 7.1.2 ToBoolean( argument )
这个抽象操作的的转换方式惨遭表10
##### 表10：
|argument类型|结果|
|---|------|
|Undefined|返回false|
|Null|返回false|
|Boolean|返回argument|
|Number|返回false,如果argument是+0，-0，NaN，其他情况一律返回true|
|String|返回falser,如果argument是一个空字符串（length是0）,其他情况一律返回true|
|Symbol|返回true|
|Ojbect|返回true|

#### 7.1.3 ToNumber( argument )
这个抽象操作的的转换方式惨遭表11
##### 表11：
|argument类型|结果|
|---|------|
|Undefined|返回NaN|
|Null|返回+0|
|Boolean|argument是true，返回1，argument是false返回+0|
|Number|返回argument|
|String|参照下文的转换算法步骤|
|Symbol|抛出TypeError异常|
|Ojbect|运用以下步骤：1. 设primValue是ToPrimitive(argument,hint Number).2. 返回ToNumber(primValue)|

##### 7.1.3.1 ToNumber运用于String类型
对字符串应用ToNumber时，对输入字符串应用如下文法，如果此文法还是无法把字符串解释为*字符串数值常量*，结果将会是NaN。
>注意1:这个语法中的符号都是由Unicode编码组成，如果String包含UTF-16编码之外补充的编码形式或者任何不接受的编码形式，都将返回NaN

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
>注意2：注意字符串数值常量和数值常量有一些不同：
  * 字符串数值常量中的 / 符号之前或者之后可能会有空白和行终结符
  * 十进制的字符串数值常量可能会有多个0在前面
  * 十进制的字符串数值常量可能会有+ 或者 - 符号前缀
  * 字符串数值常量空或者只包含空白符会转换成 +0
  * Infinity和-infinity被认为是字符串数值常量，不是数值常量

###### 7.1.3.1.1 运行过程


