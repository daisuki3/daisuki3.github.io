---
layout: post
title: 聊一聊Javascript类型转换
subtitle: Symbol.toPrimitive,valueOf,toString,
date: 2020-11-26
author: yh
catalog: true
tags:
    - javascript
    - Front End
---

先来看几个常见的js类型转换问题
```js
[] == 0
[] == ![]
0 == {}
{} == !{}
1 + {}
1 + []
```
如果没接触过类型转换，你现在肯定是一头雾水。

这个过程中涉及到**从对象到原始类型**的隐式转换。

那么js内部的隐式转换是如何运行的呢？

ES6中，优先级最高的转换方法是`[Symbol.toPrimitive](hint)`
其中hint有三种类型
1. string
2. number
3. default

当涉及到隐式类型转换时，js首先会去调用对象内部的这个方法。

我们之后再聊这个方法不存在时js会如何去做，先来看看这个方法的行为。

```js
var test = {
    str: 'test',
    num: 10,

    
    [Symbol.toPrimitive](hint){
        console.log(`${hint}`)
        return hint == 'string' 
        ? `{str: ${this.str}}` 
        : this.num
    }
}

//在chrome浏览器中测试。

test > 1
//number
//true
test == ![]
//default
//false
1 + test
//default
//11
[test].join('')
//string
//"{str:test}"
```
可以看到不同语境下传给`Symbol.toPrimitive`的`hint`参数

那么如果`Symbol.toPrimitive`不存在呢？

js会尝试调用toString和valueOf来得到原始值。如果两个方法返回的都不是原始值，就会抛出`Uncaught TypeError: Cannot conver object to primitive value`

那么调用顺序呢？当然不是想当然的先`valueOf`再`toString`。

而是根据hint来决定`toString`和`valueOf`这两个方法的执行顺序。

string： toString -> valueOf

number或者default: valueOf -> toString

看个例子
```js
var test = {
    str: 'test',
    num: 10,

    valueOf(){
        console.log('valueOf')
        return 10
    },

    toString(){
        console.log('toString')
        return '10'
    }
}

test == 1
//valueOf
//false
test + 1
//valueOf
//11
[test].join('')
//toString
//'10'
```
可以看到hint为string，也就是`[test].join('')`时，`valueOf`从未被调用。


现在再来看开头的例子。

`[] == 0`

先去调用`valueOf`，返回`[]`，继续，`toString`返回 `''`

`''` vs `0`

是的，现在问题又来了，如何进行不同原始值类型间的隐式转换呢？

- 比较 

    如果有Number，另外一边也转换成Number

    如果两边都是String，String.charCodeAt()转化后再比较

- 相加

    如果有String，另一个也转换成String

看例子
```js
'' == 0
//true 左边Number('')得到0
0 > '1'
//false
'b' > 'a'
//true charCodeAt转换
'b' > 1
//false
'b' <= 1
//false 这两个都是false因为'b'转换成Number是NaN

1 + {}
//'1{object object}' 1被转换成了String
```

现在我们再回头看开头的例子
```js
[] == ![]
/*
右边会进行Boolean转换 Boolean([]) true -> false

左边valueOf [] -> toString 得到'' Boolean('') -> false

所以结果为true
*/

0 == {}
/*
右边valueOf然后toString得到{object object}最后Number转换是NaN

结果为false
*/

{} == !{}
/*
右边Boolean然后取反得到false
左边对{object object}取布尔得到true

结果为false
*/

1 + {}
/*
右边valueOf然后toString -> '{object object}'
左边也要String -> '1'
最后得到'1{object object}'
*/

1 + []
/*
右边valueOf然后toString -> ''
左边String -> '1'
最后得到'1'
*/
```

总结一下

- 对象到原始类型的隐式转换
    
    1. Symbol.toPrimitive(hint)，该方法不存在时，才需要valueOf()或toString()来得到原始类型
    
    2. 根据hint有不同的调用顺序
     
        hint == 'string' -> 先toString()然后valueOf() 
       
       hint == 'number' 或者 'default' -> 先valueOf()然后toString()
       如果这两个都调用过了还是没得到原始类型，就抛出`Uncaught TypeError: Cannot conver object to primitive value`

- 原始类型间的隐式转换
    
    1. 比较

        有Number时另一边转换成Number
        
        都为String时charCodeAt()后比较

    2. 相加

        有String时另一个也转换成String 