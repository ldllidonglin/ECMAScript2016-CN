## 第十一章 词法语法
ECMAScript脚本或者模块首先会被转换为一序列的输入元素，这些元素都是tokens、行终结符、注释、空格。代码是从左往右扫描的，并且来回扫描得到可能得到的最长的代码单元。  

当处理输入元素时，识别其中的词法会有多种情况.这就需要多种目标符号来匹配词法语法. InputElementRegExpOrTemplateTail 目标符号是在当上下文中允许RegularExpressionLiteral、TemplateMiddle、TemplateTail 时使用. InputElementRegExp 目标符号是在当上下文中允许 RegularExpressionLiteral 但不允许 TemplateMiddle时, 或者允许 TemplateTail. InputElementTemplateTail目标符号是在当上下文中允许 TemplateMiddle 或者 TemplateTail 允许 但是不允许 RegularExpressionLiteral。其他情况下使用 InputElementDiv

> 注意：当使用多个目标符号时，必须确保没有模棱两可的符号，因为它被自动插入封号规则影响。比如，现在有一个上下文语法有一个除法或者除法赋值，并且允许RegularExpressionLiteral 目标符号。那么他将会被自动插入分号导致影响。如下面所示：
  ```
  a = b
  /hi/g.exec(c).map(d);
  ```
在行终结符后面的空白符、没有注释的代码单元。并且上下文语法中允许除法或者除法赋值，并且在行终结符钱没有分号。那么上面的例子就会变成如下：
  ```
  a = b / hi / g.exec(c).map(d);
  ```
##### 语法
```
InputElementDiv::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    DivPunctuator
    RightBracePunctuator
InputElementRegExp::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    RightBracePunctuator
    RegularExpressionLiteral
InputElementRegExpOrTemplateTail::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    RegularExpressionLiteral
    TemplateSubstitutionTail
InputElementTemplateTail::
    WhiteSpace
    LineTerminator
    Comment
    CommonToken
    DivPunctuator
    TemplateSubstitutionTail
```














