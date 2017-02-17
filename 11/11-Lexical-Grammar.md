## 第十一章 词法语法
### 11.9 自动插入分号
几乎所有的ECMAScript语句和声明都需要以分号结尾。这样的分号一般都是直接出现在代码中。然而，为了便利，在某些情况下这些分号会在代码中省略。这些情况下分号会被自动加入到源代码的token流中。
#### 11.9.1 自动插入分号规则
在一下的规则中，“token”是指根据11章中描述的词法符号由当前词法环境识别的。
有三条主要的基本规则：

1. 当从左到右解析程序代码，遇到一个任何产生式也无法识别的token，只要满足以下三个条件之一，就会在这个token之前插入一个分号：
  * 这个token被至少一个行终结符和前一个token分开
  * 这个token是 }
  * 这个token是 ) 并且插入分号后会被解析为do-while语句的结尾分号
2. 当从左到右解析程序代码，当输入流结束，整个输入流没法被解析为完整的ECMAScript脚本或者模块，那在输入流的末尾会自动插入分号
3. 当从左到右解析程序代码，遇到一个被部分产生式解析的token，但是这些产生式是*受限产生式*，在受限产生式里紧跟在行终结符或者非行终结符后的第一个token被称作受限token。当至少一个行终结符把这个token和前一个token分割开的时候，会在受限token前插入分号

然而，这有一个附加的优先条件：如果插入分号会导致语句是空语句或者插入的分号是for语句的中两个分号之一，那这个分号不会被插入。

>以下是受限产生式语法：
```
UpdateExpression[Yield]:
  LeftHandSideExpression[?Yield][no LineTerminator here]++
  LeftHandSideExpression[?Yield][no LineTerminator here]--
ContinueStatement[Yield]:
  continue;
  continue[no LineTerminator here]LabelIdentifier[?Yield];
BreakStatement[Yield]:
  break;
  break[no LineTerminator here]LabelIdentifier[?Yield];
ReturnStatement[Yield]:
  return;
  return[no LineTerminator here]Expression[In, ?Yield];
ThrowStatement[Yield]:
  throw[no LineTerminator here]Expression[In, ?Yield];
ArrowFunction[In, Yield]:
  ArrowParameters[?Yield][no LineTerminator here]=>ConciseBody[?In]
YieldExpression[In]:
  yield[no LineTerminator here]*AssignmentExpression[?In, Yield]
  yield[no LineTerminator here]AssignmentExpression[?In, Yield]
```

这些受限产生式的实际效果：
* 当 ++ 或者 -- 被当作后缀运算符时，并且被至少一个行终结符把 ++ 或者 -- 和之前的token分隔开，那将会插入一个分号在 ++ 或者 -- 之前
* 当continue，break，return，throw，yield这些token在遇到下一个token前遇到一个行终结符，那在这些token之后会插入一个分号

对ECMAScript程序员的影响是：
* 后缀运算符 ++ 或者 -- 应该和它的操作数在同一行
* return、throw语句的表达式或者在yield后的赋值表达式应该和return、throw、yield在同一行
* 在break或者continue中的标签标识符应该和break或者continue在同一行

#### 11.9.2 自动插入分号示例
源代码：
```
{ 1 2 } 3
```
在ECMAScript语法中是不合法语句，甚至在自动插入分号规则下也是不合法的，作为对比，源代码：
```
{ 1 
2 } 3
```
也是一个不合法的ECMAScript语句，但是运用自动插入分号规则后，得到如下：
```
{ 1 
;2 ;} 3;
```
这就是一个合法的ECMAScript语句。
源代码:
```
for (a; b
)
```
是不合法的ECMASCript语句，并且不会被自动插入分号，因为for语句的头部需要分号，自动插入分号规则是永远不会插入for语句头部的那两个分号的
源代码：
```
return
a + b
```
会被自动插入分号规则改成如下：
```
return;
a + b;
```
>注意1 表达式a + b不会通过return语句当作返回值，因为在return和它之间被一个行终结符分割开了
源代码：
```
a = b
++c
```
会被自动插入分号规则改成如下：
```
a = b;
++c;
```
>注意2 ++ 这个token不会被当作后缀操作符运用于b，因为b和++之间有一个行终结符
源代码：
```
if ( a > b)
else c = d
```
这不是一个合法的ECMAScript语句,并且不会在else之前插入token，因为插入的话会导致空语句
源代码：
```
a = b + c
(d + e).print()
```
不会被自动插入分号，因为第二行的括号表达式可以理解为一个函数调用的参数列表：
```
a = b + c(d+e).print()
```
在一个复制语句北徐以左括号开始的情况下，最好的处理方式是在语句结束的地方加明确的添加一个分号，而不是依靠自动插入分号规则















