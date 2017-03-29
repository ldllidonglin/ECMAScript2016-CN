# 第四章 概述
本节包含对 ECMAScript 语言非规范性的概述  

ECMAScript 是在宿主环境中执行计算、处理对象的面向对象编程语言。就如这里所定义的那样，ECMAScript语言并不是一种计算机上‘自给自足’的语言。事实上，本规范没有任何针对输入外部数据或输出计算结果的条文。相反，我们期望 ECMAScript 程序的宿主环境不仅可提供本规范中描述的对象和其它基础设施，还能提供某些特定环境下的 宿主 (host) 对象。除了说明宿主对象应该提供可被 ECMAScript 程序访问的某些属性，可被调用的某些方法外，关于它的其他描述和行为超出了本规范涉及的范围。

ECMAScript最初是被设计为一种脚本语言，但是现在已经被广泛的使用成为一种通用编程语言。*脚本语言* 是一种用于操作、自定义、自动化现有系统设施的编程语言。在这种系统中，可用功能已经可以通过用户界面使用，脚本语言是暴露这些功能给程序进行控制的机制。这样说的话，现有的系统就是为完善脚本语言的功能提供了宿主对象及基础设施。脚本语言被设计成专业和非专业程序员都能使用。

ECMAScript最初被设计为一种*网络脚本语言*，其为浏览器中的网页提供了一种机制，使其更加生动，并且执行了基于客户端-服务端这样的架构下的web端的部分服务计算。ECMAScript语言现在被用来为各种宿主环境提供核心功能。因此，这里只定义了不属于任何宿主环境的核心语言规范。

ECMAScript早已不是简单脚本那么简单了，它已经在许多不同的环境和尺度全方位的使用。随着ECMAScript使用的扩展，其功能和基础设施也在不断的增加。ECMAScript现在已经是一个全功能的通用编程语言。

ECMAScript的机制和一些其他语言很类似，特别是C，Java<sup>TM</sup>，Self，和 Scheme。在以下的文献中可以找到他们的具体描述：

ISO/IEC 9899:1996, Programming Languages – C.

Gosling, James, Bill Joy and Guy Steele. The Java™ Language Specification. Addison Wesley Publishing Co., 1996.

Ungar, David, and Smith, Randall B. Self: The Power of Simplicity. OOPSLA '87 Conference Proceedings, pp. 227-241, Orlando, FL, October 1987.

IEEE Standard for the Scheme Programming Language. IEEE Std 1178-1990.