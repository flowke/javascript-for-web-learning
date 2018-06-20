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

### 3.1 自动类型转换

* 一元 `+, -` 操作符会把非数值用 Number 转换
* `*, /, -` 运算会把非数值用 Number 转换
* `+` 加法操作对于 一方是字符串的情况:
  - 另一方是字符串, 拼接
  - 如果另一方不是字符串, 转成字符串
    - 对象, 数字, bool 调用 toString() 转
    - undefined, null 调用 String() 转
* `<,<=,>,>=` 关系元素的转换:
  - 都是字符串: 使用字符编码进行比较, (字符一一对应比较, 先找出大的)
  - 如果一个是数字, 将另一个转成数字, 进行数字间比较
  - 有原始值的对象使用原始值按照上述方法比较, (使用 valueOf得到原始值)
  - 无原始值的对象(valueOf方法继承自Object.prototype), toString 后比较
  - bool 值转成数字


#### 转成数字

**Number()**

```js
// 有效转换
Number(true) // bool 转换成 0,1
Number(1) // 1
Number(null) // 0
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
Number(undefined) // NaN
// 3.无法用 Symbol进行转换, 直接抛错而不是NaN
// 4.对于对象, 查看以下情况

// 现在开始测试对象
// 会先调用 valueOf 进行转换, 如果转换无效再调用 toString
// valueOf 和 toString 可能直接继承自 Object.prototype ,
// 也可能基于 构造器自身的 prototype 上的实现

// 以下这些对象 使用 valueOf 得到原始值后进行转换
Number( new String('kkk') ) ; //Nan
Number( new String('99') ) ; //99
Number( new Boolean('99') ) ; // 1

// 对于以下这些对象会, 调用 valueOf 后, 再调用 toString, 不过基本就是 NaN
// 两个方法继承自 Object.prototype
Number( {} ); //Nan
Number( new Set([0,0]) ); //Nan
Number( new Map() ); //Nan
Number( function(){} ) //Nan


// 对于数组,
// valueOf 继承自 Object.prototype, 返回对象自身
// toString 基于自己实现, 返回元素的 字符串表示
// []: 返回空字符串: ''
// [true]: 返回 true 的字符串表示: 'true', 所以: NaN
// [undefined],[null]: 这两个值被数组 toString, 后呈现为 '' 字符串
// 两个元素以上的数组, 因为有 逗号(,), 无论如何是挽回不了了
Number( [9] ); // 9
Number( ['99'] ); // 99
Number( [undefined] ); // 0
Number( [null] ); // 0
Number( [] ); // 0
Number( [new Boolean(true)] ); // Nan
Number( [new String('990')] ); // 990
```

> 注: 一些其他算数运算的操作会使用 Number() 自动转换

**parseInt(s, radix)**

radix 用于指定解析 s 的进制数, 默认根据 s 的格式决定:

0x 开头的 s , radix 是 16

0 开头的 s, radix 在 ES3 是 8, 在 ES6 是 10

其他情况 radix 是 10


```js
// 忽略前后空格, 运行有前导 0, (正负号可以在前导 0 之前)
// 仅数字格式的字符串有效, (s为字符串和数字的包裹类依然有效)
// 数字之后的无效字符串(包括小数点)会被忽略
parseInt('-00445'); // -445
parseInt('445fff'); // 445
parseInt('  0x889' ); // 2185
parseInt('  0x889', 10); // 0
parseInt('  0x889', 16); // 2185
parseInt('  100', 8); // 64
parseInt('  044'); // 44
parseInt(' '); // NaN
```

**parseFloat(string)**

同 parseInt 大致一样, 两点不同:

> 注: 小数点后为 0 或无小数点都会返回整数

- 会解析小数点
- 值解析 10 进制

```js
parseFloat(new String('33')); // 33
parseFloat( '4.99' ); // 4.99
parseFloat( '0x889' ); // 0
parseFloat( '1.1e3' ); // 1100
```
