[TOC]

#### 数据类型

1.  基本类型(值类型, 变量存储在栈内存中)

String、Number、Boolean、Null、Undefined、Symbol(es6新增)

2.  引用类型（变量存储在堆内存和栈内存中，栈内存中存储变量的标识符和指向堆内存中该对象的指针即堆内存中该对象的地址），引用类型的值是按引用访问的

Obejct、Function、Array、RegExp、Date



>undefined与null区别以及使用场景

```
null 指代空对象指针
使用场景：
（1）释放内存
（2）空值判断

undefined 表示此处应该有一个值，但是还没有定义
使用场景：
（1）变量被声明了，但没有赋值时，就等于undefined。
（2）调用函数时，应该提供的参数没有提供，该参数等于undefined。
（3）对象没有赋值的属性，该属性的值为undefined。
（4）函数没有返回值时或者return后面什么也没有，返回undefined

初始化变量时可根据希望存放类型指定
let a = null;  // 存放对象
let b = undefined;   //存放数值
```



>symbol类型

```js
symbol用于模拟私有方法或属性， 任意一个symbol数据是一个独一无二的值

symbol属性名以及对象各类属性的属性名遍历：

let obj = Object.create({}, {
  getFoo: {
    value: function() { return this.a; },
    enumerable: false,
  }
})
obj.a = 1,
obj.b = function() {},
obj[Symbol('a')] = 2,

Object.getOwnPropertySymbols(obj); //[Symbol(a)]  遍历自身symbol类型属性
Object.getOwnPropertyNames(obj); //['a', 'b', 'getFoo']  遍历自身非symbol类型属性（包括不可枚举属性）
Reflect.ownKeys(obj);  //[Symbol(a), 'a', 'b', 'getFoo']  遍历自身所有类型属性，包括不可枚举和symbol类型
Object.keys(obj); //['a', 'b'] 遍历自身可枚举属性， 
// for...in遍历可枚举属性（包括继承而来的）, 搭配hasOwnProperty使用过滤出自身可枚举属性
```



#### typeof与instanceof区别

```javascript
typeof null({}, []);   //object
typeof 1(NaN);   // number
typeof 类名  //function
typeof 返回的类型值： 'object','number','function','boolean','undefined','symbol','string'

instanceof判断构造函数的原型是否在该对象的原型链上(即一个变量是否是某个对象的实例)
1. typeof无法区分引用类型，使用instanceof
object instanceof constructor

function test(){};
const a=new test();
a instanceof test   //true

2. 用构造函数创建的对象返回true，否则返回false, 数组例外都会返回true 
console.log(new String('dafdsf') instanceof String) //true
console.log('csafcdf' instanceof String) //false, 检查原型链会找到 undefined
console.log(new Array(1, 3, 4) instanceof Array) //true 
console.log([1,3,4] instanceof Array) //true


3. 在继承关系中，判断一个实例是否属于它的父类型
function Aoo(){}
function Foo(){} 
Foo.prototype = new Aoo();
var foo = new Foo(); 
console.log(Foo instanceof Function);//true
console.log(foo instanceof Foo)//true 
console.log(foo instanceof Aoo)//true

4. 判断数组对象类型
(1) instanceof
(2) isPrototypeOf:   Array.isPrototypeOf(()|[])
(3) constructor:  []|().constructor = Array
(4) isArray
```

