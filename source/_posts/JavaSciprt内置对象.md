---
title: JavaScript内置对象
date: 2017-03-04 18:57:46
tags: 
- JavaScript 
- 前端
---

#### Number

有四个属性包含在Number对象里：

* MAX_VALUE
* MIN_VALUE
* NEGATIVE_INFINITY
* POSITIVE_INFINITY

MAX_VALUE属性返回1.7976931348623157e+308，它是 JavaScript 能够处理的最大数字：

```
Number.MAX_VALUE
1.7976931348623157e+308
```
MIN_VALUE属性返回5e-324 值，这是 JavaScript 中最小的数字：

```
Number.MIN_VALUE
5e-324
```
NEGATIVE_INFINITY 是 JavaScript 能够处理的最大负数，表示为 -Infinity：

```
Number.NEGATIVE_INFINITY
-Infinity
```

POSITIVE_INFINITY 属性是大于 MAX_VALUE 的任意数，表示为 Infinity：

```
Number.POSITIVE_INFINITY
Infinity
```

#### Boolean

Boolean是一个代表true或false值的对象。

相当于false的值

* 0
* -0
* null
* ''
* NaN

其他的布尔值相当于true

#### String

String对象是文本值的包装器。String对象不需要进行实例化便能够使用。String对象只有一个属相：length，它是只读的。

String的部分方法列表：

* charAt
* charCodeAt
* concat
* fromCharCode
* indexOf
* lastIndexOf
* match
* replace
* search
* slice
* split
* substr
* substring
* toLowerCase
* toUpperCase

#### Date

Date对象提供了一种方式来处理日期和时间

在没有参数的情况下对其进行实例化：

```
const myDate = new Date()
```

传递milliseconds作为一个参数：

```
var myDate = new Date(milliseconds)
```

将一个日期字符串作为一个参数传递：

```
var myDate = new Date('2017/03/04')
```

Date的方法列表

* getDate
* getDay
* getFullYear
* getHours
* getMilliseconds
* getMinutes
* getMonth
* getSeconds
* getTime
* getTimezoneOffset

getDate 方法返回一个月份的天数，范围从 1 到 31；getDay 方法返回每周的天数，范围从 0 到 6， 这里 0 是从星期日起算的； getHours 方法返回小时数值， 范围从 0 到 23；getMilliseconds 函数返回毫秒数值，范围从 0 到 999。 getMinutes 和 getSeconds 方法返回一个范围从 0 到 59 的值，getMonth 方法返回一个 从 0 到 11 之间的月份数值。本列表中惟一独特的方法 是 getTime 和 getTimezoneOffset。 getTime 方法返回 自 1/1/1970 中午 12 点的毫秒数，而 getTimezoneOffset 方法返回 格林尼治标准时间和本地时间之间的时间差，以分钟为单位。

对于大多数getterf方法， 还有setter方法，接受相应值范围内的数值参数。

* setDate
* setFullYear
* setHours
* setMilliseconds
* setMinutes
* setMonth
* setSeconds
* setTime

对于上述所有getter方法，有一些匹配的方法返回相同范围，只是这些值以国际标准时间设置。

* getUTCDate
* getUTCDay
* getUTCFullYear
* getUTCHours
* getUTCMilliseconds
* getUTCMinutes
* getUTCMonth
* getUTCSeconds

当然，又有对所有掩饰的getter都有setter方法，对于国际标准时间也一样。这些方法包括：

* setUTCDate
* setUTCFullYear
* setUTCHours
* setUTCMilliseconds
* setUTCMinutes
* setUTCMonth
* setUTCSeconds

Date对象有一些方法课将日期转换成一个字符串：

toDateString方法将日期转化成字符串：

```
var myDate = new Date()
undefined
myDate.toDateString()
"Sat Mar 04 2017"
```

toTimeString方法将时间从Date对象转换为字符串：

```
var myDate = new Date()
undefined
myDate.toTimeString()
"19:59:08 GMT+0800 (CST)"
```

有几种方法使用区域设置将日期转换成字符串：

* toLocaleDateString
* toLocaleTimeString
* toLocaleString

```
var myDate = new Date()
undefined
myDate.toLocaleTimeString('en-US')
"7:59:08 PM"
myDate.toLocaleTimeString('zh-CN')
"下午7:59:08"
myDate.toLocaleTimeString('jp-JP')
"下午7:59:08"
```

#### Array
JavaScript中的对象用于保存或修改一系列的元素。数组的长度和其中的元素不是固定的。由于数组的长度可写，有时数组的元素可能出现在不同的位置上。

* concat 
* every
* fill
* filter
* find
* findIndex
* forEach
* indexOf
* join
* lastIndexOf
* map
* pop
* push
* reduce
* reverse
* shift
* slice
* some
* sort
* splice

concat 用于合并两个数组，但并不会改变原来的数组，结果以新的数组返回。

every 用来测试数组数组中的所有元素是否通过了测试，结果以true/false的形式返回。

fill 在指定的区间内为数组填充值，具体要填充的元素区间是[star, end)，start和end都可以是负数，当它们为负数时它的索引值会被计算成 length + star t或 length + end ，其中length是数组的长度。

filter 用于测试所有的元素。返回的结果是一个包含满足测试元素的新的数组。如果没有数组满足要求则返回空数组。

find 和 findIndex 由于查找满足测试的第一个元素，其中 find 返回的是元素而 findIndex 返回的是索引。对于未找到的值，前者返回 undefined 后者返回 -1。

indexOf 和 lastIndexOf 返回数组中可以找到给定元素的第一个/最后一个索引，如果不存在，则返回-1。

join 将所有元素用指定的字符串连接起来，默认使用 ',' 即是逗号。

map 方法创建一个新的数组，该数组中的元素是执行了 map 中回调的函数的值。

pop 方法从数组中删除最后一个元素，并返回该元素的值。此方法更改数组的长度。

push 方法将一个或多个元素添加到数组的末尾，并返回数组的新长度。

reduce 方法对累加器和数组的每个值 (从左到右)应用一个函数，以将其减少为单个值。

reverse 方法颠倒数组中元素的位置，并返回该数组的引用。

shift 方法从数组中删除第一个元素，并返回该元素的值。此方法更改数组的长度。

slice 方法返回指定区间的新的数组。具体的区间是[start, end)。star 和 end 都可以为负数。如果 start/end 为负数那么 -N 代表院士数组的的倒数第N个元素。

some 方法测试数组中的某些元素是否通过了指定函数的测试。返回值true/false，some会对第一个满足测试条件的值返回true，否者返回false。

splice  方法通过删除现有元素和/或添加新元素来更改数组的内容。


#### Math

JavaScript Math 对象用于执行 数学函数。它不能加以实例化：Math 对象的原样使用它，在没有任何实例的情况下从该对象调用属性和 方法：

```
var PI = Math.PI
```

Math 对象有许多属性和方法 向 JavaScript 提供数学功能。所有的 Math 属性都是只读常量，包括：

* E 
* LN2
* LN10
* LOG2E
* LOG10E
* PI
* SQRT1_2
* SQRT2

除了这些属性，还有几个方法可以用来返回一个数的不通值。其中每种方法都接受值，并根据方法名称返回一个值。

* abs
* cos
* sin
* tan
* asin
* acos
* atan
* sqrt
* pow
* exp

有三种方法可用于JavaScript的取整:

* ceil
* floor
* round