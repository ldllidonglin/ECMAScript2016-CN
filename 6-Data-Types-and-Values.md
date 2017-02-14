# 第六章 ECMAScript Data Types and Values
在这个规范中的算法所操作的各种值都有相对应的类型，本章就是定义这些值的类型。类型又再分为ECMAScript语言类型和规范类型。  
在这个规范中，“Type（x）”是“the type of x”的简写，其中的“type”就是在本章中定义的ECMAScript语言类型和规范类型。如果是没有任何类型的值，就用"empty"来指代
## 6.1 ECMAScript语言类型
ECMAScript语言类型就是指可以直接在由ECMAScript编写的程序中操作使用的值的类型，有以下几种：Undefined,Null,Boolean,String,Symbol,Number,Object。ECMAScript语言值有一一对应的ECMAScript语言类型
### 6.1.1 Undefined类型
Undefined类型只有一种值，就是undefined，任何变量在没有被赋值之前都有undefined值
### 6.1.2 Null类型
Null类型只有一种值，就是null
### 6.1.3 Boolean类型
Boolean 类型表示逻辑实体，有两个值，true 和 false。
### 6.1.4 String类型
The String type is the set of all ordered sequences of zero or more 16-bit unsigned integer values (“elements”) up to a maximum length of 253-1 elements.字符串类型通常被用于表示文本数据，这时String当中的每一个元素都被当作UTF-16编码的值。每个元素被当作一个在这个序列中的占位符。序列中的位置索引都是非负数。第一个元素的索引值是0，第二个元素是1，按此排列。String的length值就是其元素的个数，空字符串的length是0，所以它不包含任何元素。  
在ECMAScript中解释运行String值时，其每一个元素都被当作UTF-16编码，然而，ECMAScript没有对String当中的元素作任何编码限制，所以他们可能被错误的解释运行。Operations that do not interpret String contents treat them as sequences of undifferentiated 16-bit unsigned integers。String.prototype.normalize（21.1.3.12）函数能按照指定的规则正规化String值。String.prototype.localeCompare（21.1.3.10）会在函数内把String值正规化。没有其他操作会隐式的正规化String值。只有显示指定规则或者本地语言规定才会发生这样的操作。
