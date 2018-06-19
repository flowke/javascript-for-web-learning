# 数据类型

- Undefined
- Null
- Boolean
- Number
- String
- Symbol
- Object

前六种是有原始值的类型

## 1. 初始化

## 2. 类型判断

#### 2.1 typeof

```js
typeof 1 // number
typeof 'ff' // string
typeof true // boolean
typeof undefined // undefined

typeof function(){} // function
typeof Symbol() // symbol

typeof null // object
typeof {} // object
typeof [] // object
typeof /k/ // object

```

**使用 typeof 可以可靠的判断出: undefined, number, string, boolean, symbol**

null, {}, [], 都会返回字符串 object

function 的类型存在兼容性, 正则表达式对象在 Safari5 和 Chrome7 会返回 function


#### 2.2 借用 Object.prototype.toString()


```js
Object.prototype.toString.call(undefined) // [object Undefined]
Object.prototype.toString.call(null) // [object Null]
Object.prototype.toString.call('str')); // [object String]
Object.prototype.toString.call(true); // [object Boolean]
Object.prototype.toString.call(1); // [object Number]
Object.prototype.toString.call({}); // [object Object]
Object.prototype.toString.call([]); // [object Array]
Object.prototype.toString.call(()=>{}); // [object Function]
Object.prototype.toString.call(/k/); // [object RegExp]
Object.prototype.toString.call(Symbol()); // [object Symbol]
```

#### 2.3 判断对象类型

- instanceof
- 利用constructor

#### 2.4 使用能力检查

## 3. 类型转换

#### 转成数字

**Number()**

```js
// 有效转换
Number(true) // bool 转换成 0,1
Number(1) // 1
Number(null) // 0
Number([]) // 0, 特例
// 只包含 整数,浮点, 16进制格式 数字符串转换成 10 进制, 数字前可以有正负号
// 数字的前导 0 会被忽略, 可以有正负号, 但必须出现在前导 0 之前
// 两边的空格会被完全忽略
Number('222') // 222
Number('00222') // 222
Number('0xf') // 15
Number('-00004.1') // -4.1
Number(' ') // 0:  空字符串 或 只有空格的字符串会转换成 0

// 无效转化， 以下所有情况都会转成 NaN
// 1.除上述的所有字符串
// 2.undefined
// 3.无法用 Symbol进行转换, 直接抛错而不是NaN
//
// 4.对于对象, 查看一下情况
// 先重写 valueOf 和 toString, 以查看这两个方法的调用情况
let valueOf = Object.prototype.valueOf;
let toString = Object.prototype.toString;

Object.prototype.valueOf = function(...args){
  console.log('call valueOf');
  return valueOf.call(this, ...args);
}
Object.prototype.toString = function(...args){
  console.log('call toString');
  return toString.call(this, ...args);
}

// 现在开始测试对象
//
// 对于以下这些有原始值的对象, 直接使用原始值进行转换, valueOf 和 toString 不会执行
Number( new String('kkk') ) ; //Nan
Number( new String('99') ) ; //99
Number( new Boolean('99') ) ; // 1

// 对于以下这些对象会, 调用 valueOf 后, 再调用 toString, 不过基本就是 NaN
Number( {} ); //Nan
Number( new Set([0,0]) ); //Nan
Number( new Map() ); //Nan

// 对于函数, 只调用 valueOf,  结果直接返回 NaN
Number( function(){} ) //Nan


// 对于数组, 只会调用 valueOf, 然后直接返回转换后的值
Number( [9] ); // 9
Number( ['99'] ); // 99
Number( [undefined] ); // 0
Number( [null] ); // 0
Number( [] ); // 0
Number( [new Boolean(true)] ); // Nan 嘿嘿 奇怪吧
Number( [new String('990')] ); // 990
Number( new Boolean('ll') ); // NaN
// 如果数组有两个元素或以上, 直接返回 NaN
```
