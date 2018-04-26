# 正则表达式

## 元字符
|符号|含义|注意事项|
|:--:|:--:|:--:|
|\d|匹配一个数字字符|等价于 [0-9]|
|\D|匹配一个非数字字符|等价于 [^0-9]|
|\w|匹配字母、数字、下划线|等价于[A-Za-z0-9_]|
|\W|匹配非字母、数字、下划线|等价于[^A-Za-z0-9_]|
|\s|匹配任何空白字符，包括空格、制表符、换页符等等|等价于 [ \f\n\r\t\v]|
|\S|匹配任何非空白字符|等价于 [^ \f\n\r\t\v]|

## 字符集 (范围)
|符号|含义|注意事项|
|:--:|:--:|:--:|
|x\|y|匹配 x 或 y|'(z\|f)ood' 则匹配 "zood" 或 "food"|
|[xyz]|字符集合|匹配所包含的任意一个字符|
|[x-z]|字符范围|匹配指定范围内的任意字符,==[xyz]|
|[^xyz]|负值字符集合|匹配未包含的任意字符|
|[^x-z]|负值字符范围|匹配任何不在指定范围内的任意字符,==[^xyz]|

## 限定符 (数量)
限定符用来指定正则表达式的一个给定组件必须要出现多少次才能满足匹配。有 * 或 + 或 ? 或 {n} 或 {n,} 或 {n,m} 共6种。

|符号|含义|注意事项|
|:--:|:--:|:--:|
|{n,m}|最少匹配 n 次且最多匹配 m 次| m 和 n 均为非负整数，其中 n <= m |
|{n,}|至少匹配 n 次| n 是一个非负整数 |
|{n}|匹配确定的 n 次|n 是一个非负整数|
|?|匹配前面的子表达式零次或一次|等价于 {0,1}|
|+|匹配前面的子表达式一次或多次|{1,}|
|\*|匹配前面的子表达式零次或多次|{0,}|

## 定位符 (边界)
定位符使您能够将正则表达式固定到行首或行尾。

|符号|含义|注意事项|
|:--:|:--:|:--:|
|^|匹配输入字符串开始的位置||
|$|匹配输入字符串结尾的位置||

## 
|符号|含义|注意事项|
|:--:|:--:|:--:|
|(pattern)|||



#### 参考资料：
[正则表达式 - 教程](http://www.runoob.com/regexp/regexp-tutorial.html)

[正则测试](http://tool.chinaz.com/regex)
