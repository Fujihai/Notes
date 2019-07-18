# 前言

## 对象声明方式

JavaScript 中声明对象的方式：

* 字面式声明
* New
* 构造方法声明
* 工厂方法声明
* 原型模式声明
* 混合模式声明

## 面向对象相关知识

* 遍历属性和方法
* 封装、原型、原型链
* 继承：原型继承、构造函数继承、call()、apply()

* 对象冒充

## 面向对象概述

面向对象是对代码的一种抽象，对外统一提供调用接口的编程思想。

## 基于原型的面向对象

基于原型的面向对象方式中，对象 (object) 则是依靠构造器 (constructor) 利用原型 (prototype) 构造出来的。

> 一切皆对象。

## 面向对象名词解释

属性：事物的特性。

方法：事物的功能。

对象：事物的一个实例。

原型：js 函数对象中的 prototype 属性引用了一个对象，即原型对象 (原型)。

> JavaScript 中，所有函数都是对象，它有一个 prototype 属性，引用(指向)了一个原型对象。

```javascript
属性：事物自身拥有的东西。
如：人 -> 名字，身高，年龄 | 汽车 -> 长，宽，高，颜色

方法：事物的功能。
如：人 -> 学习，玩耍，唱歌 | 汽车 -> 拉人，载货

对象：众多人中的一个，众多汽车中的一辆
```

### prototype

```javascript
function F() {};
console.log(F.prototype);	// => {constructor: ƒ}
console.log(F.prototype instanceof Object); // => true
```

> F.prototype 是一个引用，它指向了内存中的一个地址，这个地址存储的是一个对象，即原型对象。
>
> Object 是 JavaScript 中所有对象的父对象。

### 构造函数对象

函数对象：使用函数构造器创建，在 C++ 中就是构造函数。

```javascript
var obj = new Function(arg1, arg2,..., functionBody());
```

> 所有的参数都是字符串形式。arg1，arg2 是函数的参数，最后一个参数是函数体。前面参数都会按照顺序依次填入 functionBody 的参数中。此种方式效率低。

```javascript
var add = new Function("a", "b", "return a + b");
var s = add(2, 5);
console.log(s);
```

> line 1 ~ 2：等价于创建一个函数：function add(a, b) { return a + b };
>
> ​					line 2 中，add(2, 5) 会先调用，再返回一个结果值，并赋值给 s。

# 闭包

闭包：闭包是一个拥有许多变量和绑定了这些变量的环境的表达式(通常是一个函数)。

## 相关知识

变量作用域分两种：全局作用域、局部作用域。

```javascript
var n = 999;
function f1() {
    console.log(n);
}
f1(); //=> 999；

function f2() {
    var temp = 9990;
}
alert(temp); //=> 无法访问 f2 的局部变量 temp
```

> JavaScript 中，由于作用域链，函数内部可以读取全局变量。函数外部无法读取到函数内部的局部变量。
>
> 函数内使用 var 声明的变量是局部变量，否则，就是全局变量。

## 访问函数局部变量的解决方案

使用 **闭包** 解决访问函数局部变量的问题。

```javascript
function a() {
    var i = 0;
    function b() {
        console.log(++i);
    }
    return b;
}

var c = a(); //=> c = b
c();	//=> b(); => 1
```

> line 9：a() 调用返回的是内部函数 b 的引用，c = b，c 内部函数 b 的引用。
>
> line 10: c() 等价于 b() 函数调用。
>
> 特点：函数 b 是在 a 函数内嵌套的，函数 a 需要返回函数 b。
>
> 用途：1. 读取函数内部变量 
>
> ​			2. 让  局部变量 i 的值 保留在内存中

```javascript
function f1(){
    var n = 999;
    nAdd = function() {
        n = n + 1;
    }
    function f2() {
        console.log(n);
    }
    return f2;
}

var rs = f1();
rs();	//=> 999
nAdd(); //=> 正常执行
rs();	//=> 1000
```

> 首先，在 f1 函数内部，nAdd 函数没有使用 var 声明，它是全局作用域上的函数。但由于作用域链的原因，所以它可以访问 n。
>
> line 13 & 15：rs 是 f2 的闭包函数。此外，可以看出 n 一直存在于内存中，就算 f1 调用结束后也没有销毁，						所以它的值会改变。f1 是 f2 的父函数，在 f1 中，n 对于 f2 来说就像全局变量，所以 f2 依赖						于 f1，两个函数一直都存在于内存中。

## 闭包优缺点

优点：有利于封装，可以访问局部变量

缺点：内存占用浪费严重，有内存泄漏的问题。

# 对象声明方式

## 字面量式声明对象

```javascript
var person = {
    name: 'zhangshan',
    age: 26,
    sex: 'man',
    eat: function(fds) {
        console.log('我在吃' + fds);
    },
    play: function(ga) {
        console.log('我在玩' + ga);
    }
};

consolelog(person.name);	//=> zhangshan
person.eat('面条');		//=>我在吃面条
console.log(person instanceof Object);	//true
```

## new Object() 方式

```javascript
var box = new Object();

box.name = "zhangshan";
box.age = 100;
box.infos = function(str) {
    return this.name + '---' + this.age + '---' + str; //this 当前对象
};
```

> Object 是所有对象的基类，根，所有的 javascript 对象都是由 Object 延伸。

## 构造函数声明对象

```javascript
function person(name, sex, age) {
    this.name = name;		
    this.sex = sex;
    this.age = age;
    
    this.show = function() {
        console.log(this.name + '---' + this.sex + '---' + this.age);
    }
}

var p = new person("zhangshan", 'male', 18);
p.show();
```

> line 11: this 是指向当前对象。this 是指向 p。再调用 show 方法时，p.name，p.sex，p.age。构造函数内部    		      只能用 this 访问属性和方法。

## 工厂方式声明对象

```javascript
function createObject(name, age) {
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.run = function () {
      	return this.name + '---' + this.age + '运行中...';  
    };
    return obj;
}

var p1 = createObject('zhangshan', 18);
var p2 = createObject('lisi', 20);
```

> 工厂模式需要一个专门用于创建对象的函数，在工厂函数中创建对象并返回。
>
> line 6 ： 对象属性的访问一定要使用 this 关键字。**this 指向调用者本身**

**任何模式下，同种模式中的创造出来的对象都是独立存在的。**

## 构造方式与工厂方式异同

* 构造方式会显式创建对象，属性和方法都是赋值给 this，不需要 return 对象。
* 工厂在方法内部创建 object 对象，并返回 object 对象，属性和方法都是赋给 object 对象。

## 原型模式声明对象

**任何 js 方法或函数，都自带一个 prototype 属性，且它以对象方式存在。**

```javascript
function box() {}

box.prototype.color = "red";
box.prototype.height = "1.7";
box.prototype.width = "1.2";
box.prototype.show = function() {
    console.log(this.color + '---' + this.height + '---' + this.width);
}

var b = new box();

conoel.log(box.prototype);	//=>{constructor: ƒ}
console.log(box.prototype instanceof Object);	//=> true
```

> 使用原型模式声明可以让所有实例化的对象都拥有它包含的属性及方法。
>
> prototype 是每个函数自带的属性，原型模式声明对象就是往函数的 prototype 的原型对象上添加属性与方法，本质上就是往对象上添加属性与方法。

```javascript
function test(){}

test.prototype = {
    color: 'red',
    height: '1.7',
    width: '1.2'
    show: function() {
        console.log(this.color + '---' + this.height + '---' + this.width);
    }
}

var t = new test();
```

## 混合模式声明对象

混合模式即为：构造模式 + 原型模式。构造模式用于设置属性，原型模式用于添加方法。

```javascript
function test(v1, v2, v3) {
    this.v1 = v1;
    this.v2 = v2;
    this.v3 = v3;
}

//方式一
test.prototype = {
    show: function() {
        
    }
}

//方式二
test.prototype.方法名称  = function() {
    //执行代码
}

var obj = new test(v1, v2, v3);
obj.show();
```

> 某种程度上，可将 函数声明 看成 类 的声明。

## 对象遍历及存储

对象有时候可以当做数组来进行处理。

```javascript
var people = {
    name: 'zhangshan',
    age: '18',
    height: '180',
    showInfo: function() {
        console.log(people.name);
    }
};

for(var i in people) {
    console.log(i);		//=> name,age,height,showInfo
    console.log(people[i]);	//=> 输出对象的属性值或者方法体
}
```

> 在遍历对象时，i 的值取的是对象所有的属性名称和方法名称。
>
> 直接声明的对象，可以使用对象遍历的方式获取数据。

```javascript
function people() {
    this.name = "zhangshan";
    this.age = "18";
    this.height = "180";
    this.demo = function() {
        console.log(this.name);
    }
}

var p = new people();

for(var i in people) {
    console.log(i);		//=> name,age,height,showInfo
    console.log(people[i]);	//=> 输出对象的属性值或者方法体
}
```

> 使用构造函数声明的对象要实例化才可以进行遍历。
>
> line 1 ~ 8：相当于类的定义，要真正实例化对象出来才可以进行遍历。

## 对象在内存中的分布

内存分为 **数据段，栈内存，堆内存及代码段四个部分** 。

```javascript
var people = {};
people.name = "zhangshan";
people.age = "18";
people.show = function() {
    console.log(this.name);
}
```

> 栈内存中存放的是 引用变量 people ，它是一个引用地址，是一个16进制的值。
>
> 堆内存中存放的是对象 people 的属性及相关方法名。
>
> 代码段存放的是方法体，堆内存中的方法名指向代码段中存放的方法体。

# 封装

封装(Encapsulation) : 把对象内部数据和操作细节进行隐藏。

> 大多数面向对象的语言都支持封装的特性，提供了 private 关键字来隐藏某些属性或方法，用来限制被封装的数据或者内容的访问，只对外提供一个对象的专门访问的接口，此接口一般是一个调用方法，通过该方法来访问被隐藏的数据。

**JavaScript 中没有上述特性，但它可以通过闭包来实现封装。**

> 函数内部声明的变量。外部是访问不到的。
>
> 公有与私有内容的区别是：能否在对象外部直接访问。能 -> 公有，不能 -> 通过接口方法，是私有的内容。

```javascript
function demo() {
    var n = 1;
    function test() {
        return ++n;
    }
    /*
    this.test = function() {
    	return ++n;
    };
    */
    return test;
}

console.log(demo()());
```

> n 是 demo 函数内的局部作用域，无法直接访问，通过返回能访问局部变量的函数 test，从而达到外部函数访问局部变量的目的。
>
> 此例中，test 也称特权方法。

```javascript
function A() {
    function _xx() { console.log(11 + '****'); }
    this.xx = function() {
        return _xx;
    }
}

A.prototype = {
    oth: function() {
        console.log("普通方法");
    }
};

var a = new A();
var b = a.xx(); 
b();  //=> 11****
```

> line 14 : 实例化对象 a。
>
> line 15 : a.xx() 返回 _xx 函数体，b 就是 _xx 的函数本体。
>
> line 16 : b() 调用等价于 _xx 调用。

# 继承

## 原型继承

原型：是利用 **prototype** 添加属性和方法。

原型链：js 在创建对象(不论是普通对象还是函数对象) 的时候，都有一个叫做 `__proto__` 的内置属性，用于指向创建它的函数对象的原型对象 prototype，并通过原型和原型链引出继承的概念。

```java
var person = function(){};
var p = new person();

console.log(person.prototype instanceof Object);	//true
console.log(p.__proto__ instanceof Object);			//true
console.log(p.__proto__ === person.prototype);		//true
```

> line 2 : 实例化对象过程：
>
> 1.  var p = {};		//p 是个空对象
> 2. p.`__proto__` = person.prototype ;  //`__proto__` 是 js 函数自带的一个属性
> 3. 初始化对象 p  //等价于 person.call(p)
>
> 假如需要去查找 p.name 属性，会先去 p.`__proto__` 中查找，若找不到，由于 p.`__proto__`  的一个对象引用，所以会查找 p.`__proto__.__proto__` ，这就是原型链。

```javascript
var person = function(){};
person.prototype.say = function() {
    console.log("天气挺好的");
}
var p = new person();	//天气挺好的
p.say();
```

> line 5 : p 只是一个变量，但由于 p = new person，p.`__proto__` 等于 person.prototype。
>
> line 6 : 调用 p.say() 方法时，p 变量本身没有 say 方法，所以会去 p.`__proto__` ，也就是 person.prototype 			上寻找 say 方法，最后，调用到了 person.prototype.say 方法。

### 原型链实现过程

```javascript
var person = function(){};
person.prototype.say = function() { 
	console.log('天气挺好的');
};
person.prototype.age = 18;

var programmer = function() {};
programmer.prototype = new person();
programmer.prototype.wcd = function() {
    console.log("明天天气也不错！");
}
programmer.prototype.age = 22;

var p = new programmer();
p.say();		//=> 天气挺好的
p.wcd();		//=> 明天天气也不错
console.log(p.age);  //=> 22
```

> line 14：等价于  p.`__proto__` = programmer.prototype; 
>
> line 8：programmer.prototype = new person(); 等价于下面两句：
>
> ​			var p1 = new person(); 
>
> ​			programmer.prototype =  p1。
>
> line 15：p.say() 方法调用，会经过以下查找过程： 
>
> 			1. p本身没有 say 方法。 p.`__proto__` -> programmer.prototype 
>    			2. 由于 programmer.prototype 与 临时变量 p1 相等，会查找 p1，p1 本身也无 say 方法。
>       			3. 因 p1.`__proto__` = person.prototype，最后，找到 person.prototype.say() 方法。
>
> 以上就是原型链的实现过程。

可以从类的继承上分析此例，person 是父类， programmer 是子类，通过原型指向 `programmer.prototype = new person()`，实现子类指向父类，子类与父类若存在相同的属性，则子类会覆盖掉父类的属性值。

## 构造继承

在子类内部构造父类的对象实现继承。

```java
function parents(name) {
    this.name = name;
    this.say = function() {
        console.log("父类名字：" + this.name);
    }
}

function child(name, age) {
    this.pObj = parents;
    this.pObj(name);
    this.age = age;
    this.sayC = function() {
        console.log("子类名字: " + this.name + '---' + "age: " + this.age);
    }
}

var p = new parents("zhangshan");
p.say();
var c = new child("李四", 20);
c.sayC();
```

> line 9 - 10：继承 parents，子类属性指向父类并调用父类构造函数。
>
> 父函数对象被子函数对象继承，所有的属性和方法，都将传递到子对象中。

## apply 与 call 实现继承

对象内置方法中的 apply 和 call 都可用于继承，区别在于传参不同。

call & apply：调用一个对象的一个方法，以另一个对象替换当前对象。

### 使用

```javascript
call  -> obj.call(方法， var1, var2, var3);
apply -> obj.apply(方法，[var1, var2, var3]);
```

### call 继承

```javascript
function person(name, age, height){
    this.name = name;
    this.age = age;
    this.height = height;
    this.say = function() {
        console.log(this.name + ":" + this.age + ":" + this.height);
    }
}

//call 继承
function student(name, age) {
    person.call(this, name, age);
}

//apply 继承
function teacher(name, age, height) {
    person.apply(this, [name, age, height]);
}

var p = new person("张珊", 25, 170);
p.say();		//张珊 25 170
var stu = new student("李四", 18);
stu.say();		//=> 李四 18 undefined
var t = new teacher("王五", "23", "170");
teacher.say();
```

> line 16 : 实参 李四 及 18 都传递进 student 的构造函数中。
>
> line 11 :  将 李四 、18  及 this 都传递进 person 的构造函数中，this 指向的是 外部的 stu 变量。
>
> line 2~ 4 : this 就是指 student 中传递过来的 this，this.name 就是 stu.name ， 值为李四，this.age 就是	stu.age ，值为18，this.height 就是 stu.height，值为 undefined。

# 关键词

js 面向对象关键词： instanceof / delete / call / apply / arguments / callee / this

## instanceof 

判断变量是否是某一对象的实例

```javascript
var arr = new Array();
console.log(arr instanceof Array);	//=> true
console.log(arr instanceof Object);	//=> true

function test(){}
var obj = new test();
console.log(obj instanceof test);	//=> true
console.log(obj instanceof Object);	//=> true
```

> 所有对象本质上都是继承于 Object

## delete

删除对象属性。注意：delete 不能删除原型链中的属性和变量。

```javascript
function fun() {
    this.name = "zhangshan";
    this.say = function() {
        console.log(this.name);
    }
}

var obj = new fun();
console.log(obj.name);	//=> zhangshan
delete obj.name;
console.log(obj.name);	//=> undefined
obj.say();
delete obj.say;			//=> 删除对象的方法
obj.say();
```

> line 1 : 此行代码等价于定义了一个类。

## call & apply

传递参数不同

```javascript
function add(a, b) {
    console.log(a + b);
}

function subs(a, b) {
    console.log(a - b);
}

add.call(subs, 5, 3);		//=>8
add.apply(subs, 5, 3);		//=>8
```

> 第一个参数只能引用一个存在的对象。

```javascript
function animal() {
    this.name = "ani";
    this.showName = function() {
        alert(this.name);
    }
}

function cat(){
    this.name = "cat";
}

var an = new animal();
var c = new cat();
an.showName.call(c, ",");	//=>cat
an.showName.call(c, []);	//=>cat
```

> 通过 call 方法，将 animal 的 showName 方法给了 cat 使用了。
>
> line 14 & 15：showName 方法中的 this 指向了 c，所以最后打印的是 cat.name 方法。

## callee

callee 是 arguments 的一个属性，它返回正在执行的 function 对象，即函数体。

```javascript
function demo() {
    console.log(arguments.callee);	//callee 当做属性，函数内容
    console.log(arguments.callee()); //递归无终止条件，陷入死循环，报错
}
demo();	//=>demo() {console.log(arguments.callee);}
```

## arguments

每个函数都有一个 Arguments 对象的实例 arguments，引用函数的参数(实参)。

可以用数组下标方式引用 arguments 元素。

arguments.length 是参数个数。

arguments.callee 引用函数自身。

## this

```javascript
var x = 1;
function test() {
    this.x = 0;		
}
test();		
console.log(x);	//=> 0
```

> line 3 : 在函数内部，未使用 var 声明的变量就是全局变量。

```javascript
function test() {
    this.name = "zhangshan";
    this.age = 18;
}
console.log(t.name);
```

> line 2 & 3 : this 表示当前对象。

call, apply, this 第一个参数

```javascript
var x = 0;
function test() {
    console.log(this.x);
}

var o = {};
o.x = 1;
o.m = test;
o.m.apply(); //=> 0
o.m.apply(o); //=>1
```

# 对象冒充

将父类的属性和方法「非 prototype 上」一起传给子类作为特权属性和特权方法。

```javascript
function person(name, age){
    this.name = name;
    this.age = age;
    this.sayHi = function() {
        console.log("hi");
    }
}
person.prototype.walk = function() {
    console.log("walk.....");
}
function student(name, age, grade) {
    this.newMethod = person;
    this.newMethod(name, age);
    this.grade = grade;
}
var s1 = new student("zhangshan", 15, 5);
s1.sayHi();
s1.walk();	//报错
```

> line 12 : 冒充 person 对象，传递特权属性和特权方法「line 2-6」给子类。
>
> line 16 : s1 是 student 对象，继承 person，拥有 person 所有属性和方法。
>
> 注意： s1 继承了 person 中的特权方法和属性，没有继承共有方法和属性 「person.prototype」上的属性与			方法。