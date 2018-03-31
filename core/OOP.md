# JavaScript 的面向对象编程

在 ES6 之前, JavaScript 没有 class.

JS 使用函数来充当实例的类, 事实上也叫构造函数. 连概念都有点乱, 但情况确实是这样.

看一个创建对象的案例:

```js
// 定义对象类型
function Man(name) {
  // 实例的属性
  this.name = name;
}

// 实例化出一个对象
let sal = new Man('Sal');

sal.name; // 'Sal'

```

对象的属性会覆盖 prototype 上的同名属性, 但只在当前对象有用

`new Man()` 后会做以下事情:

1. 创建了一个新对象, 这个对象继承了 `Man.prototype`.
2. this 会绑定到新创建的对象上, 构造函数被调用. new Man 和 new Man() 是一样的.
3. 构造函数返回的对象就是结果. 构造函数不显式返回一个对象，则使用步骤1创建的对象。
