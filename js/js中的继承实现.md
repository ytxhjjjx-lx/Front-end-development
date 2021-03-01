[TOC]

js中的继承是基于原型的

#### 原型链继承

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



#### 构造函数继承

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



#### 组合继承（常用）

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



#### 原型式继承

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



#### 寄生组合式继承

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



#### ES6类继承

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

