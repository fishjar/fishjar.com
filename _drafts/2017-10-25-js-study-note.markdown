---
layout: post
title:  "JavaScript笔记"
tags:
  - JS
  - JavaScript
---


# JavaScript笔记



## 变量提升

```
console.log(a);
var a = 1;
// 相当于
var a;
console.log(a);
a = 1;
// 变量提升只对var命令声明的变量有效
console.log(b); // 报错: ReferenceError: b is not defined
b = 1;
```



## 条件语句
switch语句后面的表达式与case语句后面的表示式，
在比较运行结果时，采用的是严格相等运算符（===）
```
var x = 1;

switch (x) {
  case true:
    console.log('x发生类型转换');
  default:
    console.log('x没有发生类型转换');
}
// x没有发生类型转换
```


## 数据类型
```
typeof 123 // "number"
typeof '123' // "string"
typeof false // "boolean"

typeof NaN // 'number'
NaN === NaN // false
[NaN].indexOf(NaN) // -1
Boolean(NaN) // false
isNaN(NaN) // true
isNaN('Hello') // true
// 相当于
isNaN(Number('Hello')) // true

function f() {}
typeof f
// "function"

typeof undefined
// "undefined"

typeof {} // "object"
typeof [] // "object"
typeof null // "object"
```
typeof可以用来检查一个没有声明的变量，而不报错
```
// 错误的写法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正确的写法
if (typeof v === "undefined") {
  // ...
}
```

> null是一个表示”无”的对象，转为数值时为0；

> undefined是一个表示”无”的原始值，转为数值时为NaN




## 转义

这些字符前面加上反斜杠，都表示特殊含义

> \0 null（\u0000）
> \b 后退键（\u0008）
> \f 换页符（\u000C）
> \n 换行符（\u000A）
> \r 回车键（\u000D）
> \t 制表符（\u0009）
> \v 垂直制表符（\u000B）
> \' 单引号（\u0027）
> \" 双引号（\u0022）
> \\ 反斜杠（\u005C）

反斜杠还有三种特殊用法

```
'\251' // "©"
'\xA9' // "©"
'\u00A9' // "©"

'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
```

如果在非特殊字符前面使用反斜杠，则反斜杠会被省略
```
'\a'
// "a"
```


## 对象

> 查看一个对象本身的所有属性，可以使用Object.keys方法。
> delete命令只能删除对象本身的属性，无法删除继承的属性
> 删除一个不存在的属性，delete不报错，而且返回true。
> in运算符不能识别，对继承的属性也返回true。
> for...in循环用来遍历一个对象的全部属性。

for...in循环...
它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性
它不仅遍历对象自身的属性，还遍历继承的属性。
如果只想遍历对象本身的属性，可以使用hasOwnProperty方法，在循环内部判断一下是不是自身的属性。

```
for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
```



## 函数
JavaScript引擎将函数名视同变量名，所以采用function命令声明函数时，整个函数会像变量声明一样，被提升到代码头部。所以，下面的代码不会报错。

```
f();
function f() {}
```

但是，如果采用赋值语句定义函数，JavaScript就会报错。

```
f();
var f = function (){};
// TypeError: undefined is not a function
```
等同于

```
var f;
f();
f = function () {};
```

name属性返回紧跟在function关键字之后的那个函数名。

```
function f1() {}
f1.name // 'f1'

var f2 = function () {};
f2.name // ''

var f3 = function myName() {};
f3.name // 'myName'
```

length属性返回函数预期传入的参数个数，即函数定义之中的参数个数

```
function f(a, b) {}
f.length // 2
```

## 函数作用域

作用域（scope）指的是变量存在的范围。
Javascript只有两种作用域：
一种是全局作用域，变量在整个程序中一直存在，所有地方都可以读取；
另一种是函数作用域，变量只在函数内部存在。



## 函数内部的变量提升

```
function foo(x) {
  if (x > 100) {
    var tmp = x - 100;
  }
}

// 等同于
function foo(x) {
  var tmp;
  if (x > 100) {
    tmp = x - 100;
  };
}
```

## 函数本身的作用域
函数本身也是一个值，也有自己的作用域。
它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在的作用域无关。

```
var a = 1;
var x = function () {
  console.log(a);
};

function f() {
  var a = 2;
  x();
}

f() // 1
```

```
function foo() {
  var x = 1;
  function bar() {
    console.log(x);
  }
  return bar;
}

var x = 2;
var f = foo();
f() // 1
```

## 参数

函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是传值传递（passes by value）。
这意味着，在函数体内修改参数值，不会影响到函数外部。

但是，如果函数参数是复合类型的值（数组、对象、其他函数），传递方式是传址传递（pass by reference）。
也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值。


可以通过arguments对象的length属性，判断函数调用时到底带几个参数。




## 闭包

```
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```

```
function Person(name) {
  var _age;
  function setAge(n) {
    _age = n;
  }
  function getAge() {
    return _age;
  }

  return {
    name: name,
    getAge: getAge,
    setAge: setAge
  };
}

var p1 = Person('张三');
p1.setAge(25);
p1.getAge() // 25
```

## 立即调用的函数表达式（IIFE）

```
(function(){ /* code */ }());
// 或者
(function(){ /* code */ })();
```

## 运算符

```
1 > NaN // false
1 <= NaN // false
'1' > NaN // false
'1' <= NaN // false
NaN > NaN // false
NaN <= NaN // false
```


## 位运算符

> 或运算（or）：符号为|，表示若两个二进制位都为0，则结果为0，否则为1。

> 与运算（and）：符号为&，表示若两个二进制位都为1，则结果为1，否则为0。

> 否运算（not）：符号为~，表示对一个二进制位取反。

> 异或运算（xor）：符号为^，表示若两个二进制位不相同，则结果为1，否则为0。

> 左移运算（left shift）：符号为<<，详见下文解释。

> 右移运算（right shift）：符号为>>，详见下文解释。

> 带符号位的右移运算（zero filled right shift）：符号为>>>，详见下文解释。


将任意数值转为32位整数

```
function toInt32(x) {
  return x | 0;
}
```
对一个整数连续两次“否运算”，得到它自身。能达到取整效果。

```
~~2.9 // 2
~~47.11 // 47
~~1.9999 // 1
~~3 // 3
```

“异或运算”有一个特殊运用，连续对两个数a和b进行三次异或运算，aˆ=b, bˆ=a, aˆ=b，可以互换它们的值

```
var a = 10;
var b = 99;

a ^= b, b ^= a, a ^= b;

a // 99
b // 10
```

异或运算也可以用来取整。

```
12.9 ^ 0 // 12
```

左移运算符表示将一个数的二进制值向左移动指定的位数，尾部补0，即乘以2的指定次方（最高位即符号位不参与移动）。
如果左移0位，就相当于将该数值转为32位整数，等同于取整，对于正数和负数都有效。

```
13.5 << 0
// 13

-13.5 << 0
// -13
```

右移运算符表示将一个数的二进制值向右移动指定的位数，头部补0，即除以2的指定次方（最高位即符号位不参与移动）。
右移运算可以模拟2的整除运算。

```
5 >> 1
// 相当于 5 / 2 = 2

21 >> 2
// 相当于 21 / 4 = 5

21 >> 3
// 相当于 21 / 8 = 2

21 >> 4
// 相当于 21 / 16 = 1
```


## void运算符

void运算符的作用是执行一个表达式，然后不返回任何值，或者说返回undefined。

```
<a href="javascript:void window.open('http://example.com/')">点击打开新窗口</a>
```


```
<a href="http://example.com" onclick="f();return false;">文字</a>
<a href="javascript: void(f())">文字</a>
```

下面的代码会提交表单，但是不会产生页面跳转。

```
<a href="javascript: void(document.form.submit())">文字</a>
```


## 一元运算符

会把运算子转成数值。

```
+'abc' // NaN
-'abc' // NaN
+true // 1
-false // 0
```


## Error对象

```
var err = new Error('出错了');
err.message // "出错了"
```
```
function throwit() {
  throw new Error('');
}

function catchit() {
  try {
    throwit();
  } catch(e) {
    console.log(e.stack); // print stack trace
  }
}

catchit()
// Error
//    at throwit (~/examples/throwcatch.js:9:11)
//    at catchit (~/examples/throwcatch.js:3:9)
//    at repl:1:5
```


## 原生错误类型

> SyntaxError是解析代码时发生的语法错误。

> ReferenceError是引用一个不存在的变量时发生的错误。

> RangeError是当一个值超出有效范围时发生的错误。主要有几种情况，一是数组长度为负数，二是Number对象的方法参数超出范围，以及函数堆栈超过最大值。

> TypeError是变量或参数不是预期类型时发生的错误。比如，对字符串、布尔值、数值等原始类型的值使用new命令，就会抛出这种错误，因为new命令的参数应该是一个构造函数。

> URIError是URI相关函数的参数不正确时抛出的错误，主要涉及encodeURI()、decodeURI()、encodeURIComponent()、decodeURIComponent()、escape()和unescape()这六个函数。

> eval函数没有被正确执行时，会抛出EvalError错误。该错误类型已经不再在ES5中出现了，只是为了保证与以前代码兼容，才继续保留。


## 自定义错误

```
function UserError(message) {
   this.message = message || "默认信息";
   this.name = "UserError";
}

UserError.prototype = new Error();
UserError.prototype.constructor = UserError;


new UserError("这是自定义的错误！");
```

## throw语句

```
// 抛出一个字符串
throw "Error！";

// 抛出一个数值
throw 42;

// 抛出一个布尔值
throw true;

// 抛出一个对象
throw {toString: function() { return "Error!"; } };

// 最好的做法是使用throw语句手动抛出一个Error对象。
throw new Error('出错了!');
```

throw语句还可以抛出用户自定义的错误。
```
function UserError(message) {
  this.message = message || "默认信息";
  this.name = "UserError";
}

UserError.prototype.toString = function (){
  return this.name + ': "' + this.message + '"';
}

throw new UserError("出错了！");
```


## try…catch结构

```
openFile();

try {
  writeFile(Data);
} catch(e) {
  handleError(e);
} finally {
  closeFile();
}
```


## Object 对象

Object.keys方法和Object.getOwnPropertyNames方法很相似，一般用来遍历对象的属性。
它们的参数都是一个对象，都返回一个数组，该数组的成员都是对象自身的（而不是继承的）所有属性名。

它们的区别在于，
Object.keys方法只返回可枚举的属性（关于可枚举性的详细解释见后文），
Object.getOwnPropertyNames方法还返回不可枚举的属性名。

```
var a = ["Hello", "World"];

Object.keys(a)
// ["0", "1"]

Object.getOwnPropertyNames(a)
// ["0", "1", "length"]
```


Object.getOwnPropertyDescriptor()：获取某个属性的attributes对象。
Object.defineProperty()：通过attributes对象，定义某个属性。
Object.defineProperties()：通过attributes对象，定义多个属性。
Object.getOwnPropertyNames()：返回直接定义在某个对象上面的全部属性的名称。

Object.preventExtensions()：防止对象扩展。
Object.isExtensible()：判断对象是否可扩展。
Object.seal()：禁止对象配置。
Object.isSealed()：判断一个对象是否可配置。
Object.freeze()：冻结一个对象。
Object.isFrozen()：判断一个对象是否被冻结。

Object.create()：该方法可以指定原型对象和属性，返回一个新的对象。
Object.getPrototypeOf()：获取对象的Prototype对象。



## 对象实例

valueOf()：返回当前对象对应的值。valueOf方法的作用是返回一个对象的“值”，默认情况下返回对象本身。
toString()：返回当前对象对应的字符串形式。
toLocaleString()：返回当前对象对应的本地字符串形式。
hasOwnProperty()：判断某个属性是否为当前对象自身的属性，还是继承自原型对象的属性。
isPrototypeOf()：判断当前对象是否为另一个对象的原型。
propertyIsEnumerable()：判断某个属性是否可枚举。


```
var o = new Object();
o.valueOf() === o // true
```

空对象的toString方法，结果返回一个字符串object Object，其中第二个Object表示该值的构造函数。

```
var o = {};
o.toString() // "[object Object]"
```
实例对象可能会自定义toString方法，覆盖掉Object.prototype.toString方法。通过函数的call方法，可以在任意值上调用Object.prototype.toString方法，帮助我们判断这个值的类型。

```
Object.prototype.toString.call(value)
```


不同数据类型的Object.prototype.toString方法返回值如下。

数值：返回[object Number]。
字符串：返回[object String]。
布尔值：返回[object Boolean]。
undefined：返回[object Undefined]。
null：返回[object Null]。
数组：返回[object Array]。
arguments对象：返回[object Arguments]。
函数：返回[object Function]。
Error对象：返回[object Error]。
Date对象：返回[object Date]。
RegExp对象：返回[object RegExp]。
其他对象：返回[object Object]。

也就是说，Object.prototype.toString可以得到一个实例对象的构造函数。
利用这个特性，可以写出一个比typeof运算符更准确的类型判断函数。

```
var type = function (o){
  var s = Object.prototype.toString.call(o);
  return s.match(/\[object (.*?)\]/)[1].toLowerCase();
};

type({}); // "object"
type([]); // "array"
type(5); // "number"
type(null); // "null"
type(); // "undefined"
type(/abcd/); // "regex"
type(new Date()); // "date"
```

在上面这个type函数的基础上，还可以加上专门判断某种类型数据的方法。

```
['Null',
 'Undefined',
 'Object',
 'Array',
 'String',
 'Number',
 'Boolean',
 'Function',
 'RegExp'
].forEach(function (t) {
  type['is' + t] = function (o) {
    return type(o) === t.toLowerCase();
  };
});

type.isObject({}) // true
type.isNumber(NaN) // true
type.isRegExp(/abc/) // true
```

## 数组

```
var arr = new Array(2);
// 等同于
var arr = Array(2);
```

```
new Array([1]) // [Array[1]]

// 多参数时，所有参数都是返回的新数组的成员
new Array(1, 2) // [1, 2]
new Array('a', 'b', 'c') // ['a', 'b', 'c']
```

```
var arr = new Array(3);
arr.length // 3

arr[0] // undefined
arr[1] // undefined
arr[2] // undefined

0 in arr // false
1 in arr // false
2 in arr // false
```

Array.isArray方法用来判断一个值是否为数组。它可以弥补typeof运算符的不足。

```
var a = [1, 2, 3];

typeof a // "object"
Array.isArray(a) // true
```


## 数组实例方法

valueOf方法返回数组本身。

```
var a = [1, 2, 3];
a.valueOf() // [1, 2, 3]
```

toString方法返回数组的字符串形式。
```
var a = [1, 2, 3];
a.toString() // "1,2,3"

var a = [1, 2, 3, [4, 5, 6]];
a.toString() // "1,2,3,4,5,6"
```

push方法用于在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度。
注意，该方法会改变原数组。

```
var a = [1, 2, 3];
var b = [4, 5, 6];

Array.prototype.push.apply(a, b)
// 或者
a.push.apply(a, b)

// 上面两种写法等同于
a.push(4, 5, 6)

a // [1, 2, 3, 4, 5, 6]
```
push方法还可以用于向对象添加元素，添加后的对象变成类似数组的对象，即新加入元素的键对应数组的索引，并且对象有一个length属性。

```
var a = {a: 1};

[].push.call(a, 2);
a // {a:1, 0:2, length: 1}

[].push.call(a, [3]);
a // {a:1, 0:2, 1:[3], length: 2}
```

pop方法用于删除数组的最后一个元素，并返回该元素。注意，该方法会改变原数组

对空数组使用pop方法，不会报错，而是返回undefined。

```
[].pop() // undefined
```

join方法以参数作为分隔符，将所有数组成员组成一个字符串返回。如果不提供参数，默认用逗号分隔。

concat方法用于多个数组的合并。它将新数组的成员，添加到原数组的尾部，然后返回一个新数组，原数组不变。

除了接受数组作为参数，concat也可以接受其他类型的值作为参数。它们会作为新的元素，添加数组尾部。

```
[1, 2, 3].concat(4, 5, 6)
// [1, 2, 3, 4, 5, 6]

// 等同于
[1, 2, 3].concat(4, [5, 6])
[1, 2, 3].concat([4], [5, 6])
```

如果不提供参数，concat方法返回当前数组的一个浅拷贝。

shift方法用于删除数组的第一个元素，并返回该元素。注意，该方法会改变原数组。

unshift方法用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，该方法会改变原数组。

reverse方法用于颠倒数组中元素的顺序，返回改变后的数组。注意，该方法将改变原数组。

slice方法用于提取原数组的一部分，返回一个新数组，原数组不变。
它的第一个参数为起始位置（从0开始），第二个参数为终止位置（但该位置的元素本身不包括在内）。如果省略第二个参数，则一直返回到原数组的最后一个成员。

slice方法的一个重要应用，是将类似数组的对象转为真正的数组。

```
Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
// ['a', 'b']

Array.prototype.slice.call(document.querySelectorAll("div"));
Array.prototype.slice.call(arguments);
```

splice方法用于删除原数组的一部分成员，并可以在被删除的位置添加入新的数组成员，返回值是被删除的元素。注意，该方法会改变原数组。
splice的第一个参数是删除的起始位置，第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。

如果只是单纯地插入元素，splice方法的第二个参数可以设为0。

```
var a = [1, 1, 1];

a.splice(1, 0, 2) // []
a // [1, 2, 1, 1]
```


如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。

```
var a = [1, 2, 3, 4];
a.splice(2) // [3, 4]
a // [1, 2]
```

sort方法对数组成员进行排序，默认是按照字典顺序排序。排序后，原数组将被改变。

如果想让sort方法按照自定义方式排序，可以传入一个函数作为参数，表示按照自定义方法进行排序。该函数本身又接受两个参数，表示进行比较的两个元素。如果返回值大于0，表示第一个元素排在第二个元素后面；其他情况下，都是第一个元素排在第二个元素前面。

```
[10111, 1101, 111].sort(function (a, b) {
  return a - b;
})
// [111, 1101, 10111]

[
  { name: "张三", age: 30 },
  { name: "李四", age: 24 },
  { name: "王五", age: 28  }
].sort(function (o1, o2) {
  return o1.age - o2.age;
})
// [
//   { name: "李四", age: 24 },
//   { name: "王五", age: 28  },
//   { name: "张三", age: 30 }
// ]
```

map方法对数组的所有成员依次调用一个函数，根据函数结果返回一个新数组。
map方法还可以接受第二个参数，表示回调函数执行时this所指向的对象。
map方法不会跳过undefined和null，但是会跳过空位。

```
var arr = ['a', 'b', 'c'];

[1, 2].map(function(e){
  return this[e];
}, arr)
// ['b', 'c']
```


forEach方法与map方法很相似，也是遍历数组的所有成员，执行某种操作，但是forEach方法一般不返回值，只用来操作数据。如果需要有返回值，一般使用map方法。

forEach方法也可以接受第二个参数，用来绑定回调函数的this关键字。

```
var out = [];

[1, 2, 3].forEach(function(elem) {
  this.push(elem * elem);
}, out);

out // [1, 4, 9]
```

```
var obj = {
  name: '张三',
  times: [1, 2, 3],
  print: function () {
    this.times.forEach(function (n) {
      console.log(this.name);
    });
  }
};

obj.print()
// 没有任何输出
```
上面代码中，obj.print方法有两层this，它们的指向是不一致的。外层的this.times指向obj对象，内层的this.name指向顶层对象window（详细解释参见《面向对象编程》一章）。这显然是违背原意的，解决方法就是使用forEach方法的第二个参数固定this。

```
var obj = {
  name: '张三',
  times: [1, 2, 3],
  print: function () {
    this.times.forEach(function (n) {
      console.log(this.name);
    }, this);
  }
};

obj.print()
// 张三
// 张三
// 张三
```

forEach方法也可以用于类似数组的对象和字符串。

```
var obj = {
  0: 1,
  a: 'hello',
  length: 1
}

Array.prototype.forEach.call(obj, function (elem, i) {
  console.log( i + ':' + elem);
});
// 0:1

var str = 'hello';
Array.prototype.forEach.call(str, function (elem, i) {
  console.log( i + ':' + elem);
});
// 0:h
// 1:e
// 2:l
// 3:l
// 4:o
```


filter方法的参数是一个函数，所有数组成员依次执行该函数，返回结果为true的成员组成一个新数组返回。该方法不会改变原数组。

```
[1, 2, 3, 4, 5].filter(function (elem) {
  return (elem > 3);
})
// [4, 5]


var arr = [0, 1, 'a', false];

arr.filter(Boolean)
// [1, "a"]
```

some()，every()

这两个方法类似“断言”（assert），用来判断数组成员是否符合某种条件。

它们接受一个函数作为参数，所有数组成员依次执行该函数，返回一个布尔值。该函数接受三个参数，依次是当前位置的成员、当前位置的序号和整个数组。

some方法是只要有一个数组成员的返回值是true，则整个some方法的返回值就是true，否则false。


````
var arr = [1, 2, 3, 4, 5];
arr.some(function (elem, index, arr) {
  return elem >= 3;
});
// true

var arr = [1, 2, 3, 4, 5];
arr.every(function (elem, index, arr) {
  return elem >= 3;
});
// false
```



reduce方法和reduceRight方法依次处理数组的每个成员，最终累计为一个值。

它们的差别是，reduce是从左到右处理（从第一个成员到最后一个成员），reduceRight则是从右到左（从最后一个成员到第一个成员），其他完全一样。

这两个方法的第一个参数都是一个函数。该函数接受以下四个参数。

```
[1, 2, 3, 4, 5].reduce(function(x, y){
  console.log(x, y)
  return x + y;
});
// 1 2
// 3 3
// 6 4
// 10 5
//最后结果：15
```

```
Array.prototype.sum = function (){
  return this.reduce(function (partial, value) {
    return partial + value;
  })
};

[3, 4, 5, 6, 10].sum()
// 28
```

如果要对累积变量指定初值，可以把它放在reduce方法和reduceRight方法的第二个参数。

```
[1, 2, 3, 4, 5].reduce(function(x, y){
  return x + y;
}, 10);
// 25
```
第二个参数相当于设定了默认值，处理空数组时尤其有用。

```
function add(prev, cur) {
  return prev + cur;
}

[].reduce(add)
// TypeError: Reduce of empty array with no initial value
[].reduce(add, 1)
// 1
```

indexOf方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回-1。
indexOf方法还可以接受第二个参数，表示搜索的开始位置。

lastIndexOf方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回-1。

注意，如果数组中包含NaN，这两个方法不适用，即无法确定数组成员是否包含NaN。
这是因为这两个方法内部，使用严格相等运算符（===）进行比较，而NaN是唯一一个不等于自身的值。

```
[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
```

上面这些数组方法之中，有不少返回的还是数组，所以可以链式使用。

```
var users = [
  {name: 'tom', email: 'tom@example.com'},
  {name: 'peter', email: 'peter@example.com'}
];

users
.map(function (user) {
  return user.email;
})
.filter(function (email) {
  return /^t/.test(email);
})
.forEach(alert);
// 弹出tom@example.com
```





## 包装对象

原始类型的值，可以自动当作对象调用，即调用各种对象的方法和参数。这时，JavaScript引擎会自动将原始类型的值转为包装对象，在使用后立刻销毁。

比如，字符串可以调用length属性，返回字符串的长度


```
var str = 'abc';
str.length // 3

// 等同于
var strObj = new String(str)
// String {
//   0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"
// }
strObj.length // 3
```

```
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean('') // false
Boolean(NaN) // false

Boolean(1) // true
Boolean('false') // true
Boolean([]) // true
Boolean({}) // true
Boolean(function () {}) // true
Boolean(/foo/) // true

!!undefined // false
!!null // false
!!0 // false
!!'' // false
!!NaN // false
!!1 // true
!!'false' // true
!![] // true
!!{} // true
!!function(){} // true
!!/foo/ // true
```



## Number对象

进制转换

```
(1000).toString(32) // 'v8'
parseInt('v8',32) // 1000
```

toFixed方法用于将一个数转为指定位数的小数，返回这个小数对应的字符串。

```
(10).toFixed(2) // "10.00"
10.005.toFixed(2) // "10.01"
```


## String对象

String对象提供的静态方法（即定义在对象本身，而不是定义在对象实例的方法），主要是fromCharCode()。该方法的参数是一系列Unicode码点，返回对应的字符串。

```
String.fromCharCode(104, 101, 108, 108, 111)
// "hello"
```
注意，该方法不支持Unicode码点大于0xFFFF的字符，即传入的参数不能大于0xFFFF。

```
String.fromCharCode(0x20BB7)
// "ஷ"
```
上面代码返回字符的编号是0x0BB7，而不是0x20BB7。它的根本原因在于，码点大于0xFFFF的字符占用四个字节，而JavaScript只支持两个字节的字符。这种情况下，必须把0x20BB7拆成两个字符表示。

```
String.fromCharCode(0xD842, 0xDFB7)
// "𠮷"
```

### 实例方法
charAt方法返回指定位置的字符，参数是从0开始编号的位置。
这个方法完全可以用数组下标替代。

charCodeAt方法返回给定位置字符的Unicode码点（十进制表示），相当于String.fromCharCode()的逆操作。

```
'abc'.charCodeAt(1) // 98
```

concat方法用于连接两个字符串，返回一个新字符串，不改变原字符串。

slice方法用于从原字符串取出子字符串并返回，不改变原字符串。


substring方法用于从原字符串取出子字符串并返回，不改变原字符串。它与slice作用相同，但有一些奇怪的规则，因此不建议使用这个方法，优先使用slice。


substr方法用于从原字符串取出子字符串并返回，不改变原字符串。

substr方法的第一个参数是子字符串的开始位置，第二个参数是子字符串的长度。

```
'JavaScript'.substr(4, 6) // "Script"
```

indexOf()，lastIndexOf()
这两个方法用于确定一个字符串在另一个字符串中的位置，都返回一个整数，表示匹配开始的位置。如果返回-1，就表示不匹配。两者的区别在于，indexOf从字符串头部开始匹配，lastIndexOf从尾部开始匹配。



trim方法用于去除字符串两端的空格，返回一个新字符串，不改变原字符串。


toLowerCase方法用于将一个字符串全部转为小写，toUpperCase则是全部转为大写。它们都返回一个新字符串，不改变原字符串。

localeCompare方法用于比较两个字符串。它返回一个整数，如果小于0，表示第一个字符串小于第二个字符串；如果等于0，表示两者相等；如果大于0，表示第一个字符串大于第二个字符串。
localeCompare方法会考虑自然语言的排序情况，将B排在a的前面。

```
'apple'.localeCompare('banana')
// -1

'apple'.localeCompare('apple')
// 0
```

match方法用于确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串。如果没有找到匹配，则返回null。

```
'cat, bat, sat, fat'.match('at') // ["at"]
'cat, bat, sat, fat'.match('xt') // null
```
search方法的用法等同于match，但是返回值为匹配的第一个位置。如果没有找到匹配，则返回-1。

replace方法用于替换匹配的子字符串，返回新字符串。一般情况下只替换第一个匹配（除非使用带有g修饰符的正则表达式）。


split方法按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组。
split方法还可以接受第二个参数，限定返回数组的最大成员数。
``
'a|b|c'.split('|') // ["a", "b", "c"]

'|b|c'.split('|') // ["", "b", "c"]
'a|b|'.split('|') // ["a", "b", ""]

'a|b|c'.split('|', 0) // []
'a|b|c'.split('|', 1) // ["a"]
```









## Math对象

Math.E：常数e。
Math.LN2：2的自然对数。
Math.LN10：10的自然对数。
Math.LOG2E：以2为底的e的对数。
Math.LOG10E：以10为底的e的对数。
Math.PI：常数Pi。
Math.SQRT1_2：0.5的平方根。
Math.SQRT2：2的平方根。

```
Math.E // 2.718281828459045
Math.LN2 // 0.6931471805599453
Math.LN10 // 2.302585092994046
Math.LOG2E // 1.4426950408889634
Math.LOG10E // 0.4342944819032518
Math.PI // 3.141592653589793
Math.SQRT1_2 // 0.7071067811865476
Math.SQRT2 // 1.4142135623730951
```


Math.abs()：绝对值
Math.ceil()：向上取整
Math.floor()：向下取整
Math.max()：最大值
Math.min()：最小值
Math.pow()：指数运算
Math.sqrt()：平方根
Math.log()：自然对数
Math.exp()：e的指数
Math.round()：四舍五入
Math.random()：随机数


```
Math.max(2, -1, 5) // 5
Math.min(2, -1, 5) // -1
Math.min() // Infinity
Math.max() // -Infinity
```

Math.random()返回0到1之间的一个伪随机数，可能等于0，但是一定小于1。

```
Math.random() // 0.7151307314634323
```

任意范围的随机数生成函数如下。

```
function getRandomArbitrary(min, max) {
  return Math.random() * (max - min) + min;
}

getRandomArbitrary(1.5, 6.5)
// 2.4942810038223864
```


任意范围的随机整数生成函数如下。

```
function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

getRandomInt(1, 6) // 5
```

返回随机字符的例子如下。

```
function random_str(length) {
  var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
  ALPHABET += '0123456789-_';
  var str = '';
  for (var i=0; i < length; ++i) {
    var rand = Math.floor(Math.random() * ALPHABET.length);
    str += ALPHABET.substring(rand, rand + 1);
  }
  return str;
}

random_str(6) // "NdQKOr"
```



Math.sin()：返回参数的正弦
Math.cos()：返回参数的余弦
Math.tan()：返回参数的正切
Math.asin()：返回参数的反正弦（弧度值）
Math.acos()：返回参数的反余弦（弧度值）
Math.atan()：返回参数的反正切（弧度值）



## Date对象
Date对象是JavaScript提供的日期和时间的操作接口。它可以表示的时间范围是，1970年1月1日00:00:00前后的各1亿天（单位为毫秒）。


Date对象可以作为普通函数直接调用，返回一个代表当前时间的字符串。
注意，即使带有参数，Date作为普通函数使用时，返回的还是当前时间。
```
Date()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"

Date(2000, 1, 1)
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```


Date还可以当作构造函数使用。对它使用new命令，会返回一个Date对象的实例。如果不加参数，生成的就是代表当前时间的对象。

```
var today = new Date();

today
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"

// 等同于
today.toString()
// "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
```

作为构造函数时，Date对象可以接受多种格式的参数。

new Date(milliseconds)

Date对象接受从1970年1月1日00:00:00 UTC开始计算的毫秒数作为参数。这意味着如果将Unix时间戳（单位为秒）作为参数，必须将Unix时间戳乘以1000。

new Date(datestring)

new Date(year, month [, day, hours, minutes, seconds, ms])
如果采用这种格式，最少需要提供两个参数（年和月），其他参数都是可选的，默认等于0。因为如果只使用“年”这一个参数，Date对象会将其解释为毫秒数。


```
var d1 = new Date(2000, 2, 1);
var d2 = new Date(2000, 3, 1);

d2 - d1
// 2678400000

d2 + d1
// "Sat Apr 01 2000 00:00:00 GMT+0800 (CST)Wed Mar 01 2000 00:00:00 GMT+0800 (CST)"
```



### Date对象的静态方法

```
Date.now() // 1364026285194
```

Date.parse方法用来解析日期字符串，返回距离1970年1月1日 00:00:00的毫秒数。
如果解析失败，返回NaN。

```
Date.parse('Aug 9, 1995')
// 返回807897600000，以下省略返回值

Date.parse('January 26, 2011 13:51:50')
Date.parse('Mon, 25 Dec 1995 13:30:00 GMT')
Date.parse('Mon, 25 Dec 1995 13:30:00 +0430')
Date.parse('2011-10-10')
Date.parse('2011-10-10T14:48:00')
```



### Date实例对象的方法

to类：从Date对象返回一个字符串，表示指定的时间。
get类：获取Date对象的日期和时间。
set类：设置Date对象的日期和时间。



## 正则表达式


新建正则表达式有两种方法。一种是使用字面量，以斜杠表示开始和结束。

```
var regex = /xyz/;
```

另一种是使用 RegExp 构造函数。

```
var regex = new RegExp('xyz');
```

```
var regex = new RegExp('xyz', "i");
// 等价于
var regex = /xyz/i;
```



这两种写法——字面量和构造函数——在运行时有一个细微的区别。采用字面量的写法，正则对象在代码载入时（即编译时）生成；采用构造函数的方法，正则对象在代码运行时生成。考虑到书写的便利和直观，实际应用中，基本上都采用字面量的写法。



正则对象生成以后，有两种使用方式：

> 正则对象的方法：将字符串作为参数，比如regex.test(string)。
> 字符串对象的方法：将正则对象作为参数，比如string.match(regex)。

正则对象的test方法返回一个布尔值，表示当前模式是否能匹配参数字符串。

```
/cat/.test('cats and dogs') // true
```
带有g修饰符时，可以通过正则对象的lastIndex属性指定开始搜索的位置。

```
var r = /x/g;
var s = '_x_x';

r.lastIndex = 4;
r.test(s) // false
```


正则对象的exec方法，可以返回匹配结果。如果发现匹配，就返回一个数组，成员是每一个匹配成功的子字符串，否则返回null。



### 字符串对象的方法


match()：返回一个数组，成员是所有匹配的子字符串。
search()：按照给定的正则表达式进行搜索，返回一个整数，表示匹配开始的位置。
replace()：按照给定的正则表达式进行替换，返回替换后的字符串。
split()：按照给定规则进行字符串分割，返回一个数组，包含分割后的各个成员。




### 匹配规则

点字符（.）匹配除回车（\r）、换行(\n) 、行分隔符（\u2028）和段分隔符（\u2029）以外的所有字符。

^ 表示字符串的开始位置
$ 表示字符串的结束位置

竖线符号（|）在正则表达式中表示“或关系”（OR），即cat|dog表示匹配cat或dog。

正则表达式中那些有特殊含义的字符，如果要匹配它们本身，就需要在它们前面要加上反斜杠。比如要匹配加号，就要写成\+。

\cX 表示Ctrl-[X]，其中的X是A-Z之中任一个英文字母，用来匹配控制字符。
[\b] 匹配退格键(U+0008)，不要与\b混淆。
\n 匹配换行键。
\r 匹配回车键。
\t 匹配制表符tab（U+0009）。
\v 匹配垂直制表符（U+000B）。
\f 匹配换页符（U+000C）。
\0 匹配null字符（U+0000）。
\xhh 匹配一个以两位十六进制数（\x00-\xFF）表示的字符。
\uhhhh 匹配一个以四位十六进制数（\u0000-\uFFFF）表示的unicode字符。


字符类（class）表示有一系列字符可供选择，只要匹配其中一个就可以了。所有可供选择的字符都放在方括号内，比如[xyz] 表示x、y、z之中任选一个匹配。

如果方括号内的第一个字符是[^]，则表示除了字符类之中的字符，其他字符都可以匹配。比如，[^xyz]表示除了x、y、z之外都可以匹配。

某些情况下，对于连续序列的字符，连字符（-）用来提供简写形式，表示字符的连续范围。比如，[abc]可以写成[a-c]，[0123456789]可以写成[0-9]，同理[A-Z]表示26个大写字母。



预定义模式

\d 匹配0-9之间的任一数字，相当于[0-9]。
\D 匹配所有0-9以外的字符，相当于[^0-9]。
\w 匹配任意的字母、数字和下划线，相当于[A-Za-z0-9_]。
\W 除所有字母、数字和下划线以外的字符，相当于[^A-Za-z0-9_]。
\s 匹配空格（包括制表符、空格符、断行符等），相等于[\t\r\n\v\f]。
\S 匹配非空格的字符，相当于[^\t\r\n\v\f]。
\b 匹配词的边界。
\B 匹配非词边界，即在词的内部。

```
// \s的例子
/\s\w*/.exec('hello world') // [" world"]

// \b的例子
/\bworld/.test('hello world') // true
/\bworld/.test('hello-world') // true
/\bworld/.test('helloworld') // false

// \B的例子
/\Bworld/.test('hello-world') // false
/\Bworld/.test('helloworld') // true
```

模式的精确匹配次数，使用大括号（{}）表示。{n}表示恰好重复n次，{n,}表示至少重复n次，{n,m}表示重复不少于n次，不多于m次。


量词符用来设定某个模式出现的次数。

? 问号表示某个模式出现0次或1次，等同于{0, 1}。
* 星号表示某个模式出现0次或多次，等同于{0,}。
+ 加号表示某个模式出现1次或多次，等同于{1,}。


```
// t出现0次或1次
/t?est/.test('test') // true
/t?est/.test('est') // true

// t出现1次或多次
/t+est/.test('test") // true
/t+est/.test('ttest') // true
/t+est/.test('est') // false

// t出现0次或多次
/t*est/.test('test') // true
/t*est/.test('ttest') // true
/t*est/.test('tttest') // true
/t*est/.test('est') // true
```


上一小节的三个量词符，默认情况下都是最大可能匹配，即匹配直到下一个字符不满足匹配规则为止。这被称为贪婪模式。

```
var s = 'aaa';
s.match(/a+/) // ["aaa"]
```

如果想将贪婪模式改为非贪婪模式，可以在量词符后面加一个问号。

```
var s = 'aaa';
s.match(/a+?/) // ["a"]
```


```
// 单个修饰符
var regex = /test/i;

// 多个修饰符
var regex = /test/ig;
```

g修饰符表示全局匹配（global），加上它以后，正则对象将匹配全部符合条件的结果，主要用于搜索和替换。

默认情况下，正则对象区分字母的大小写，加上i修饰符以后表示忽略大小写（ignorecase）。

m修饰符表示多行模式（multiline），会修改^和$的行为。默认情况下（即不加m修饰符时），^和$匹配字符串的开始处和结尾处，加上m修饰符以后，^和$还会匹配行首和行尾，即^和$会识别换行符（\n）。


正则表达式的括号表示分组匹配，括号中的模式可以用来匹配分组的内容。

```
/fred+/.test('fredd') // true
/(fred)+/.test('fredfred') // true
```
上面代码中，第一个模式没有括号，结果+只表示重复字母d，第二个模式有括号，结果+就表示匹配“fred”这个词。


## JSON对象

复合类型的值只能是数组或对象，不能是函数、正则表达式对象、日期对象。

简单类型的值只有四种：字符串、数值（必须以十进制表示）、布尔值和null（不能使用NaN, Infinity, -Infinity和undefined）。

字符串必须使用双引号表示，不能使用单引号。

对象的键名必须放在双引号里面。

数组或对象最后一个成员的后面，不能加逗号。



JSON.stringify方法用于将一个值转为字符串。该字符串符合 JSON 格式，并且可以被JSON.parse方法还原。


JSON.stringify方法还可以接受一个数组，作为第二个参数，指定需要转成字符串的属性。

这个类似“白名单”的数组，只对对象的属性有效，对数组无效。

第二个参数还可以是一个函数，用来更改JSON.stringify的默认行为。
这个处理函数是递归处理所有的键。

```
function f(key, value) {
  if (typeof value === "number") {
    value = 2 * value;
  }
  return value;
}

JSON.stringify({ a: 1, b: 2 }, f)
// '{"a": 2,"b": 4}'
```

JSON.stringify还可以接受第三个参数，用于增加返回的JSON字符串的可读性。如果是数字，表示每个属性前面添加的空格（最多不超过10个）；如果是字符串（不超过10个字符），则该字符串会添加在每行前面。

如果对象有自定义的toJSON方法，那么JSON.stringify会使用这个方法的返回值作为参数，而忽略原对象的其他属性。


如果传入的字符串不是有效的JSON格式，JSON.parse方法将报错。
为了处理解析错误，可以将JSON.parse方法放在try...catch代码块中。


## console对象

console.log方法用于在控制台输出信息。它可以接受多个参数，将它们的结果连接起来输出。
console.log方法会自动在每次输出的结尾，添加换行符。

如果第一个参数是格式字符串（使用了格式占位符），console.log方法将依次用后面的参数替换占位符，然后再进行输出。


%s 字符串
%d 整数
%i 整数
%f 浮点数
%o 对象的链接
%c CSS格式字符串



## 属性描述对象
```
{
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false,
  get: undefined,
  set: undefined
}
```
属性描述对象提供6个元属性。

（1）value

value存放该属性的属性值，默认为undefined。

（2）writable

writable存放一个布尔值，表示属性值（value）是否可改变，默认为true。

（3）enumerable

enumerable存放一个布尔值，表示该属性是否可枚举，默认为true。如果设为false，会使得某些操作（比如for...in循环、Object.keys()）跳过该属性。

（4）configurable

configurable存放一个布尔值，表示“可配置性”，默认为true。如果设为false，将阻止某些操作改写该属性，比如，无法删除该属性，也不得改变该属性的属性描述对象（value属性除外）。也就是说，configurable属性控制了属性描述对象的可写性。

（5）get

get存放一个函数，表示该属性的取值函数（getter），默认为undefined。

（6）set

set存放一个函数，表示该属性的存值函数（setter），默认为undefined。


Object.getOwnPropertyDescriptor方法可以读出对象自身属性的属性描述对象。

```
var o = { p: 'a' };

Object.getOwnPropertyDescriptor(o, 'p')
// Object { value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```


Object.defineProperty方法允许通过定义属性描述对象，来定义或修改一个属性，然后返回修改后的对象。
Object.defineProperty方法接受三个参数，第一个是属性所在的对象，第二个是属性名（它应该是一个字符串），第三个是属性的描述对象。比如，新建一个o对象，并定义它的p属性，写法如下。


```
var o = Object.defineProperty({}, 'p', {
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false
});

o.p
// 123

o.p = 246;
o.p
// 123
// 因为writable为false，所以无法改变该属性的值
```


如果一个属性的enumerable为false，下面三个操作不会取到该属性。

for..in循环
Object.keys方法
JSON.stringify方法


Object.getOwnPropertyNames方法返回直接定义在某个对象上面的全部属性的名称，而不管该属性是否可枚举。

```
// 比如，数组实例自带length属性是不可枚举的
Object.keys([]) // []
Object.getOwnPropertyNames([]) // [ 'length' ]

// Object.prototype对象的自带属性也都是不可枚举的
Object.keys(Object.prototype) // []
Object.getOwnPropertyNames(Object.prototype)
// ['hasOwnProperty',
//  'valueOf',
//  'constructor',
//  'toLocaleString',
//  'isPrototypeOf',
//  'propertyIsEnumerable',
//  'toString']
```

Object.prototype.propertyIsEnumerable()
对象实例的propertyIsEnumerable方法用来判断一个属性是否可枚举。

```
var o = {};
o.p = 123;

o.propertyIsEnumerable('p') // true
o.propertyIsEnumerable('toString') // false
```




## 存取器（accessor）

除了直接定义以外，属性还可以用存取器（accessor）定义。其中，存值函数称为setter，使用set命令；取值函数称为getter，使用get命令。

存取器提供的是虚拟属性，即该属性的值不是实际存在的，而是每次读取时计算生成的。利用这个功能，可以实现许多高级特性，比如每个属性禁止赋值。


```
var o = {
  get p() {
    return 'getter';
  },
  set p(value) {
    console.log('setter: ' + value);
  }
};
```

取值函数Getter不能接受参数，存值函数Setter只能接受一个参数（即属性的值）。

另外，对象也不能有与取值函数同名的属性。比如，上面的对象o设置了取值函数p以后，就不能再另外定义一个p属性。


存取器往往用于，属性的值需要依赖对象内部数据的场合。

```
var o ={
  $n : 5,
  get next() { return this.$n++ },
  set next(n) {
    if (n >= this.$n) this.$n = n;
    else throw '新的值必须大于当前值';
  }
};

o.next // 5

o.next = 10;
o.next // 10
```

存取器也可以通过Object.defineProperty定义。


```
var d = new Date();

Object.defineProperty(d, 'month', {
  get: function () {
    return d.getMonth();
  },
  set: function (v) {
    d.setMonth(v);
  }
});
```

存取器也可以使用Object.create方法定义。

```
var o = Object.create(Object.prototype, {
  foo: {
    get: function () {
      return 'getter';
    },
    set: function (value) {
      console.log('setter: '+value);
    }
  }
});
```


利用存取器，可以实现数据对象与DOM对象的双向绑定。

```
Object.defineProperty(user, 'name', {
  get: function () {
    return document.getElementById('foo').value;
  },
  set: function (newValue) {
    document.getElementById('foo').value = newValue;
  },
  configurable: true
});
```


Object.preventExtensions方法可以使得一个对象无法再添加新的属性。
Object.isExtensible方法用于检查一个对象是否使用了Object.preventExtensions方法。也就是说，检查是否可以为一个对象添加属性。
Object.seal方法使得一个对象既无法添加新属性，也无法删除旧属性。
Object.isSealed方法用于检查一个对象是否使用了Object.seal方法。
Object.freeze方法可以使得一个对象无法添加新属性、无法删除旧属性、也无法改变属性的值，使得这个对象实际上变成了常量。
Object.isFrozen方法用于检查一个对象是否使用了Object.freeze()方法。



## 对象的拷贝


```
var extend = function (to, from) {
  for (var property in from) {
    to[property] = from[property];
  }

  return to;
}

extend({}, {
  a: 1
})
// {a: 1}
```
上面这个方法的问题在于，如果遇到存取器定义的属性，会只拷贝值。

为了解决这个问题，我们可以通过Object.defineProperty方法来拷贝属性。

```
var extend = function (to, from) {
  for (var property in from) {
    Object.defineProperty(
      to,
      property,
      Object.getOwnPropertyDescriptor(from, property)
    );
  }

  return to;
}

extend({}, { get a(){ return 1 } })
// { get a(){ return 1 } })
```

这段代码还是有问题，拷贝某些属性时会失效。


可以把两种方法结合起来，对于简单属性，就直接拷贝，对于那些通过属性描述对象设置的属性，则使用Object.defineProperty方法拷贝。

```
var extend = function (to, from) {
  for (var property in from) {
    var descriptor = Object.getOwnPropertyDescriptor(from, property);

    if (descriptor && ( !descriptor.writable
      || !descriptor.configurable
      || !descriptor.enumerable
      || descriptor.get
      || descriptor.set)) {
      Object.defineProperty(to, property, descriptor);
    } else {
      to[property] = from[property];
    }
  }
}
```






































