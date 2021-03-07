[TOC]

js中的继承方式

#### 1. 原型链继承

>更改原型对象的指向实现继承

```javascript
function Film(name) {
  this.name = '你好，李焕英!';
}
Film.prototype.getName = () => this.name;

function Comedy() {
  this.type = '喜剧片';
}
// 指定父类实例为子类实例的原型
Comedy.prototype = new Film();
// new 创建新实例对象经过了以下几步：
// 1.创建一个新对象
// 2.将新对象的proto指向构造函数的prototype对象
// 3.将构造函数的作用域赋值给新对象 （也就是this指向新对象）
// 4.执行构造函数中的代码（为这个新对象添加属性）
// 5.返回新的对象
Comedy.prototype.constructor = Comedy;
Comedy.prototype.getType = () => this.type;
const comedy = new Comedy();
console.log(comedy.type);  // '喜剧片'
console.log(comedy.getName());   // '你好，李焕英!'
console.log(comedy instanceof Comedy); // true 
console.log(comedy instanceof Film); // true
console.log(comedy instanceof Object); // true, js中所有类型都继承原型链顶端Object的实例
```

优点：

1.  继承父类的属性（方法）
2.  实例既是子类的实例，也是父类的实例

缺陷：

1.  单一继承
2.  所有实例共享父类实例的引用类型属性（方法），若属性被修改会影响所有实例
3.  无法向父类构造函数传参



#### 2. 构造函数继承

> 借调父类构造函数实现继承

```javascript
function Comedy(name, type) {
  // 借调父类构造函数来增强子类实例
  Film.apply(this, name);
  this.type = type;
}
const comedy = new Comedy('你好，李焕英!', '喜剧片');
console.log(comedy.name);  // '你好，李焕英!'
console.log(comedy instanceof Comedy); // true 
console.log(comedy instanceof Film); // false
```

优点：

1.  可实现多继承，借调多个构造函数
2.  不存在引用类型属性共享问题，可以向父类构造函数传参

缺点：

1.  无法获取父类原型上的属性
2.  实例只是子类的实例



#### 3. 组合继承（常用）

>结合原型链继承和构造函数继承

```javascript
function Comedy(name, type) {
  // 借调父类构造函数来增强子类实例
  Film.apply(this, name);
  this.type = type;
}
Comedy.prototype = new Film();
Comedy.prototype.constructor = Comedy;
const comedy = new Comedy('你好，李焕英!', '喜剧片');
console.log(comedy instanceof Comedy); // true 
console.log(comedy instanceof Film); // true
```

优点：

1.  继承了父类原型的属性，实现了构造函数传参
2.  实例及是子类的实例，也是父类的实例

缺点：

调用了两次父类构造函数（耗内存）



#### 4. 原型式继承

>借助原型基于已有的对象创建新对象（相当于浅拷贝）

```javascript
function Film(obj) {
    function Comedy() {};
    Comedy.prototype = obj;
    return new Comedy();
}
const obj = {
    name: '国产电影',
    types: ['喜剧片'， '动作片'， '爱情片'],
};
const comedy1 = Film(obj);
comedy1.types.push('战争片');
const comedy2 = Film(obj);
console.log(comedy2.types); // ['喜剧片'， '动作片'， '爱情片', '战争片']


// Es6 Object.create实现同样继承效果
const comedy1 = Object.create(obj);
comedy1.types.push('战争片');
const comedy2 = Object.create(obj);
console.log(comedy2.types); // ['喜剧片'， '动作片'， '爱情片', '战争片']
```

缺点：

1. 无法向父类构造函数传参
2. 如果父类是普通对象，引用类型属性依然被子例共享



#### 5. 寄生组合式继承

>解决组合继承两次调用父类的问题

```javascript
function Comedy(name, type) {
  // 借调父类构造函数来增强子类实例
  Film.apply(this, name);
  this.type = type;
}
(function() {
    // 基于原型式继承，借助中间层构造函数实现继承，避免了组合继承中两次调用父类构造函数的问题
    const Foo = function() {};
    Foo.prototype = Film.prototype;
    Comedy.prototype = new Foo();
})()
const comedy = new Comedy('你好，李焕英!', '喜剧片');
console.log(comedy instanceof Comedy); // true 
console.log(comedy instanceof Film); // true
```

修复了组合继承的问题



#### 6. ES6类继承

>使用extends关键字，实现构造函数+原型的继承方式同等的效果

```javascript
class Film {
    constructor(name) {
        this.name = name;
    }
    getName() {
        console.log(this.name);
    }
}
console.log(typeof Film); // 'function', 类声明只是自定义的类型创建的语法糖

//ES5模拟类的实现(也称为自定义的类型创建)
function Film(name) {
    this.name = name;
}
Film.prototype.getName = () => this.name;


class Comedy extends Film {
    constructor(type) {
        super(name); // 等同于Film.call(this, name);
        this.type = type;
    }
    getType() {
        console.log(this.type);
    }
    getName() {
        super.getName(); // '你好，李焕英!', 调用父类被覆盖的方法
        console.log('重写父类方法');
    }
}
const comedy = new Comedy('你好，李焕英!', '喜剧片');
console.log(comedy instanceof Comedy); // true 
console.log(comedy instanceof Film); // true
```



#### 继承与原型链

js对象包含一个__proto__属性，大部分浏览器不支持访问，操作不慎会改变这个对象的继承原型链

使用Object.getPrototypeOf获取



(1) 基于原型链的继承:

```javascript
/** 继承属性 **/
let f = function() {
   this.a = 1;
   this.b = 2;
}
let o = new f();
f.prototype.b = 3;
f.prototype.c = 4;

原型链如下: 如控制台打印结果
// {a:1, b:2} ---> {b:3, c:4} ---> Object.prototype ---> null
console.log(o.a);  // 1
console.log(o.b);  // 2
console.log(o.c);  // 4
console.log(o.d);  // undefined

/** 继承方法 **/
const o = {
  a: 2,
  m: () => this.a + 1;
};
const p = Object.create(o);
p.a = 4;
console.log(o.m()); // 3
console.log(p.m()); // 5, 当继承的函数被调用时，this 指向的是当前继承的对象
```

(2) 生成原型链的不同方式

1. 使用语法结构创建的对象:

```javascript
var a = ["yo", "whadup", "?"];
// 数组都继承于 Array.prototype
// (Array.prototype 中包含 indexOf, map 等方法)
// 原型链:
// a ---> Array.prototype ---> Object.prototype ---> null
```

2. 使用构造器创建的对象:

```javascript
function Graph() {
  this.vertices = [];
  this.edges = [];
}
Graph.prototype = {
  addVertex: function(v){
    this.vertices.push(v);
  }
};
const g = new Graph();
// 原型链:
// g ---> Graph.prototype ---> Object.prototype ---> null
```

3. Object.create()创建的对象

```javascript
var a = {a: 1};
// a ---> Object.prototype ---> null
var b = Object.create(a);
// b ---> a ---> Object.prototype ---> null
var c = Object.create(b);
// c ---> b ---> a ---> Object.prototype ---> null
var d = Object.create(null);
// d ---> null
```

总结：

原型链查找属性比较消耗性能，查找不存在的属性时会遍历整个原型链，应避免这样的操作

```javascript
if(!g.hasOwnProperty(addVertex)) {
   不执行操作
}
```



prototype 和 Object.getPrototypeOf()

>`prototype` 是用于类的，而 `Object.getPrototypeOf()` 是用于实例的（instances），两者功能一致

```javascript
const a1 = new A(); const a2 = new A();
// Object.getPrototypeOf(a1).doSomething == Object.getPrototypeOf(a2).doSomething == A.prototype.doSomething
// a1.doSomething() 相当于执行 Object.getPrototypeOf(a1).doSomething.call(a1) == A.prototype.doSomething.call(a1)）
```

结论：

**在使用原型继承编写复杂代码之前，理解原型继承模型是至关重要的。此外，请注意代码中原型链的长度，并在必要时将其分解，以避免可能的性能问题。此外，原生原型不应该被扩展，除非它是为了与新的 JavaScript 特性兼容**

