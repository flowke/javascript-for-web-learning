# 对象

## 1. 对象认识

对象字面量:

```js
let man = {
  name: 'Joe'
}
```

#### 1.1 属性类型

对象的属性有两种类型: 数据属性, 访问器属性

**数据属性**

数据属性可以直接定义: a.b = 5;

数据属性会有4个数据特征特性来描述, 不能直接访问:

- [[Configurable]]: 是否能 delete, 修改特性, 或把数据属性改为访问器属性, 默认: false, 直接定义的默认: true
- [[Enumerable]]: 能否使用 for-in 遍历属性值, 默认: false, 直接定义的默认: true
- [[Writable]]: 属性能否修改, 默认: false, 直接定义的默认: true
- [[Value]]: 属性值, 默认: undefined

**访问器属性**

- [[Configurable]]: 同数据属性
- [[Enumerable]]: 同数据属性
- [[Get]]: 读取属性值时调用的函数, 默认 : undefined
- [[Set]]: 设置属性值时调用的函数, 默认: undefined

#### 1.2 获取或定义属性描述

**定义描述**

可以定义一个新的属性, 并且描述. 或者修改已有属性的描述.

- Object.definePropertie(obj, prop, descriptor)
- Object.defineProperties(obj, props)

**获取描述**

- Object.getOwnPropertyDescriptor(obj, prop)
  - 返回一个对象, prop 的属性描述. 没有prop 会返回undefined
- Object.getOwnPropertyDescriptors(obj)
  - 象的所有自身属性的描述符，如果没有任何自身属性，则返回空对象。

查看如下表格:

|            | configurable | enumerable | value | writable | get | set |
| ---------- | ------------ | ---------- | ----- | -------- | --- | --- |
| 数据描述符 | yes          | yes        | yes   | yes      | no  | no  |
| 属性描述符 | yes          | yes        | no    | no       | yes | yes |



如果一个描述符不具有value,writable,get 和 set 任意一个关键字，那么它将被认为是一个数据描述符。如果一个描述符同时有(value或writable)和(get或set)关键字，将会产生一个异常。

## 2. 构造函数模式中的对象

在 ES6 之前, JavaScript 没有 class.

JS 使用函数来充当实例的类, 事实上也叫构造函数. 连概念都有点乱, 但情况确实是这样.

语法: `new constructor[([arguments])]`
new 是一个运算符

用于创建自定义或内置对象实例

看一个创建自定义对象类型的案例, 在之后都使用这些代码:

```js
function Man(name) {
  this.name = name;
}

function Man2(name) {
  this.name = name;
}

// 函数会有一个 prototype , 其默认会有个constructor属性指向这个函数自身
console.log(Man.prototype); //{constructor: ƒ}

Man.prototype.say = function() {
  console.log('i am Man');
}

// 可以覆盖prototype 上默认的 constructor, 但不会修改实例的构造函数和其他特性
// 即便是重写了 constructor, 也依然是不可迭代
Man.prototype.constructor = function Cv() {
  console.log('i am another method');
}

let m1 = new Man('Joe');

console.log(m1 instanceof Man, 'type');
console.log(m1);

```

对象的属性会覆盖 prototype 上的同名属性, 但只在当前对象有用

`new Man()` 后会做以下事情:

1. 创建了一个新对象, 这个对象继承了 `Man.prototype`.
2. this 会绑定到新创建的对象上(在作用域内绑定到这个对象上), 构造函数被调用. 如果没有参数, new Man 和 new Man() 是一样的.
3. 构造函数返回的对象就是结果. 构造函数不显式返回一个对象，则使用步骤1创建的对象。

> new 和 .的优先级
>
> 带参数: 一样高 (19)
>
> 不带参数: . 高  (18)

**对象实例的 constructor 属性指向 构造函数.**

#### 2.1 原型与原型链

```js

// 每个实例会有一个内部指针[[Prototype]] 指向 对象实例化的那一刻 构造函数的 prototype
// 在浏览器实现上, 可以通过 __proto__访问 [[Prototype]] 指向的对象
console.log(m1.__proto__);
// 例如
// 如果此时
Man.prototype = {
  // constructor: Man,
  a: 4
}

let m2 = new Man('Joe');
// 则 m1上不存在 a 属性



// 实例在访问属性的时候, 会先访问实例本身, 没有再去 [[Prototype]] 指向的对象上找
console.log(m1.name);
console.log(m1.say());

// 测试对象类型
// 通过 实例的 constructor 与 构造函数对比 (constructor不被另外指定)
// 通过 instanceof 操作符,
// 用于表示 m1 能够通过 原型链找到 Man.Prototype
console.log(m1.constructor === Man, 'type');
console.log(m1 instanceof Man, 'type');
// Man.prototype.isPrototypeOf(m1)
console.log(m1 instanceof Object, 'type');

// Object.prototype.isPrototypeOf(testObj)
// 可以通过此方式测试 一个对象是否存在另一个对象的原型链上
// 例如: m1 能否通过原型链找到 Object.prototype
console.log( Object.prototype.isPrototypeOf(m1) ); // true
console.log( Man.prototype.isPrototypeOf(m1), '--0' ); // true
console.log( Man2.prototype.isPrototypeOf(m1) ); // false

// Object.getPrototypeOf()
// 返回指定对象的原型（内部[[Prototype]]属性的值
console.log( Object.getPrototypeOf(m1) );
// 返回了构造函数的 prototype

```

#### 2.2 属性相关操作

```js

// delete 删除对象中的属性
//
// Object.prototype.hasOwnProperty()
// 可以判断某个属性是不是存在于实例本身上面
console.log(m1.hasOwnProperty('name')); //true
console.log(m1.hasOwnProperty('say')); // false

// in 运算符
// 可以用于判断 这个属性能不能在对象上访问到, 包括不可枚举的组件
// 不管是实例本身上还是原型链上
console.log('name' in m1); // true
console.log('say' in m1); // true
console.log('toString' in m1); // true
console.log('constructor' in m1); // true
// 使用forin循环时, 返回对象能够访问的, 可枚举的属性
// 无法遍历直接在原型上复制的 constructor, 其 enumerable 默认为false
console.log(Object.getOwnPropertyDescriptor(m1.__proto__, 'constructor'));
// {value: ƒ, writable: true, enumerable: false, configurable: true}
console.log(Object.getOwnPropertyDescriptor(m1.__proto__, 'say'));
// {value: ƒ, writable: true, enumerable: true, configurable: true}

// 获得所有可枚举的属性, ie9+
//
// Object.keys(), 获取对象本身的 可枚举的key
// Object.getOwnPropertyNames() 获取对象本身(包括不可枚举的)key
console.log(Object.keys(m1));
console.log(Object.getOwnPropertyNames(m1.__proto__));

```

#### 2.3 继承

```js

// 继承

function SubMan(name){
  Man.call(this, name);
}

extendsAndDefine(SubMan, Man, {
  say(){
    console.log(this.name+ ' 在说话');
  }
})

// 需要使用 Object 生成是因为之后可能需要在原型上添加新的属性, 而不至于覆盖父类的原型
function extendsAndDefine(baseClass, superClass, defined){
  let proto = Object(superClass.prototype);
  proto.constructor = baseClass;

  if(Object.prototype.toString.call(defined) === '[object Object]'){
    Object.keys(defined).forEach(elt=>proto[elt]= defined[elt])
  }

  baseClass.prototype = proto;

}

let s1 = new SubMan('Flowke');

console.log(s1 instanceof SubMan, 'sub');
console.log(s1 instanceof Man, 'sub');
console.log(s1.say(), 'sub');
```
