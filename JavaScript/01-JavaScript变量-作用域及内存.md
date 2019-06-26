### 变量命名规则

JavaScript 的变量命名规则如下：

* $、_、字母、数字组成
* 不能以数字开头
* 区分大小写
* 关键字和保留字都不能当做变量名使用

### 数据类型

JavaScript 的数据类型分为两种：

基本类型：4 'str' true/false undefined null

引用类型：[] {} function

区别：基本类型值可以修改，引用类型值不可修改。

```javascript
	4->3
	var num = 4;
	num = 3;	//=> num 的值被覆盖，并未真正地把值从4变成了3

	// 在 JavaScript 中，String 是基本类型。
	var str = 'string';
	var anotherStr = str.replace('s', '');
	// replace 返回一个新的字符串，原来的字符串并没有改变。
	console.log(str + '|' + anotherStr); //=> string|tring

	var person = {};
	console.log(person);	//=> {}
	person.name = 'Xiao ming';
	person.sex = 'Male';
	person.sex = 'Female';
	delete person.sex;
	console.log(person);	//=>{name: "Xiao Ming", family: Array(2)}

	//基本类型不可添加属性
	var person = 'person';
	person.name = 'Xiao Ming';
	console.log(person.name);	//=>undefined

	//基本类型支持的方法是由其对应的包装类型所提供的
	'string'.replace
	1->Number
	'str'->String
```

> 基本类型可以看做是原子，是基本单位，不可再分。引用类型，可以看出分子，其由原子组成。

### 堆栈

JavaScript 的内存分为栈内存和堆内存。

栈内存：大小固定，有序。基本类型大小固定，所以保存在栈内存中。

堆内存：大小不固定，无序。引用类型可以动态添加属性，所以大小不固定，存放在堆内存中。

> 栈内存大小固定有序，可以直接读取。而堆内存大小不固定且无序，存放的引用类型需要一个地址来指向当前引用类型的空间。这个地址值是存放在栈内存中的。所以，保存引用类型的变量保存的并不是对象本身，而是一个指向该对象的引用地址。

```html
                 栈 内 存            堆 内 存
                +------------+   +-------------------------------+
                |            |   |                               |
                +------------+   |                               |
                |            |   |                               |
                +------------+   |                XXXXXXX        |
                |            |   |              XX      XX       |
                +------------+   |              X         XX     |
                |   person |----------------->  X   {}    XX     |
                +------------+   |              XX       XX      |
                |   name     |   |               XXX   XXX       |
                +------------+   |                  XXX          |
                |   num      |   |                               |
                +------------+   +-------------------------------+
```

### 变量比较

基本类型比较

```javascript
var xmScore = 4;
var xhScore = 4;
console.log(xmScore === xhScore);	//=> true
```

引用类型比较

```javascript
var xm = {
	age: 18,
    score: 4
};

var xh = {
    age: 18,
    score: 4
};
console.log(xm === xh);		//=>false
```

> 引用类型的比较：只有指向同一引用才相等。

```javascript
var xm = {
    age: 18,
    score: 4
};

var xh = xm;
console.log(xh === xm);		//=> true
```

判断引用类型的属性是否都相等

```javascript
var xm = {
	age: 18,
    score: 4
};

var xh = {
    age: 18,
    score: 4
};

//判断对象属性值是否相等
function equalObjs(a, b){
    for(var p in a){
        if(a[p] !== b[p]) return false;
    }
    return true;
}
console.log(equalObjs(xm, xh));		//->true

//判断数组是否相等
function equalsArrays(a, b){
    if(a.length !== b.length) return false;
    for(var i = 0,len = a.length; i < len; i++){
        if(a[i] !== b[i]) return false;
    }
    return true;
}
```

### 变量值的复制

基本类型值的复制

```javascript
var xmScore = 4;
var xhScore = xmScore;
xhScore++;
console.log(xhScore);	//5
console.log(xmScore); 	//4
```

> 结论：基本变量值的复制是值的拷贝，两者互不干涉。

引用类型值的复制

```javascript
var xm = {
    age: 18,
    score: 4
};

var xh = xm;
xh.score++;
console.log(xh.score);	//5
console.log(xm.score);	//5
```

引用类型属性的复制

```javascript
var xm = {
    age: 18,
    score: 4
};

//浅拷贝(当属性值为基本类型时有效)
//若被拷贝对象属性值为引用类型，拷贝对象与被拷贝对象会指向同一引用
function copyObj(obj){
    var newObj = {};
    for(var p in obj){
        newObj[p] = obj[p];
    }
    return newObj;
}

var xh = copyObj(xm);
console.log(xh === xm);
```

> 浅拷贝与深拷贝可以参考 jQuery 中的 extend 方法

### 参数传递

JavaScript 中的参数传递都是按值传递。

实参与形参

```javascript
// a, b 为形参
function fn(a, b){
    return a + b;
}

//1, 2 为实参
fn(1, 2);
```

基本类型的参数传递

```javascript
function addTen(num){
    //等价于 num = score
    return num + 10;
}
var score = 10;
console.log(addTen(score));	//=> 20
console.log(score);		//=>10
```

引用类型的参数传递

```javascript
function setName(obj){
    return obj.name = 'xm';
}
var person = {};
setName(person);
console.log(person.name);	//=>xm
```

> 传递的是地址值，在函数中对地址指向的空间进行修改，而 obj 与 person 指向的是同一片空间，所以对 obj 的修改也会影响到 person。

```javascript
function setName(obj){
    obj.name = 'xm';	// obj 与 person 指向同一空间
    obj = {};			// obj 指向另外一片内存空间
    obj.name = 'xh';	// obj 新指向的空间的值修改不会影响 person
}
var person = {};
setName(person);
console.log(person.name);	//=> xm
```

### 检测类型

使用 typeof 进行类型检测

```javascript
console.log(typeof 4);			//=> number
console.log(typeof(4)); 		//=> number
console.log(typeof 'str');		//=> string
console.log(typeof true);		//=> boolean
console.log(typeof undefined);	//=> undefined
console.log(typeof null);		//object
console.log(typeof []);			//=>object
console.log(typeof {});			//=>object
console.log(typeof function(){});//=>function
//对于正则对象，有的浏览器返回的是 function 
console.log(typeof /a/);		//=>object
```

使用 `A instanceof B(contructor)` 检测 A 是否是 B（B 通常是构造函数）的实例，instanceof 只能用于引用类型判断

```javascript
console.log([] instanceof Array);	//=>true
console.log([] instanceof Object);	//=>true
console.log({} instanceof Object);	//=>true
console.log({} instanceof Array);	//=>false
```

### 作用域

**含义**

```javascript
变量 
作用：起作用 
域： 区域、范围
```

> 作用域主要描述的是：变量的生命周期以及可以访问到变量的区域。

**类型**

主要分为全局作用域及局部作用域(函数作用域，也就是函数体内)。

```javascript
//name 属于全局作用域，是一个全局变量
var name = 'xm';
function fn(argument){
    //sex 属于函数体内的局部作用域
    var sex = 'male';
    console.log(name);
}
fn();
```

> 局部作用域可以访问全局作用域的变量，全局作用域不能访问局部作用域中的变量。

**JavaScipt 无块级作用域**

```javascript
//其他语言，如 Java
if(true){
   var name = 'xm';
}
console.log(name);		//=> if 块外无法访问 name 变量

//JavaScript 中无块级作用域
for(var i = 0; ; ;){
    
}

//=> 可以直接访问，因为 JavaScript 无块级作用域，所以 i 是全局作用域上面的变量，可直接访问
console.log(i);	
```

### 变量对象

每个作用域里都存在着一个 **变量对象**，作用域中的变量和方法都是该变量对象上面的属性和方法。只有全局作用域上的变量对象 window 可以直接访问。局部作用域上的变量对象都是无法直接访问的，JavaScript 引擎在后台处理时会用到。

```javascript
//变量对象
var name = 'xm';
function fn(argument){
    var sex = 'male';
    function fn2(argument){
        var age = 18;
    }
    //假设可以访问到局部作用域的变量对象，这里设置为 fn, 则
    //变量对象 fn 的属性及方法如下：
    //fn.sex 
    //fn.fn2
}
```

上述代码存在着三个作用域： 

1. 全局作用域 window, 它包含有 name 变量和 fn 函数。所有全局空间上的变量和属性都是 window 对象上的变量和属性。验证方法：

    ```javascript
   console.log(window.name = name);	//=>true
   console.log(window.fn = fn);		//=>true
   ```

 2. fn 函数形成的局部作用域，它包含有 sex 变量和 fn2 函数

 3. fn2 函数形成的局部作用域，它包含有 age 变量 

### 作用域链

作用域链用于 **变量查找**。

```javascript
var name = 'xm';
function fn(){
    var name = 'xh';
    var sex = 'male';
    function fn2(){
        var name = 'xhei';
        var age = 18;
    }
}
```

作用域链图示：

```javascript
                        window
                        +------------------------------------+
                        |                                    |
                        +--+name                             |
                        |          <--+                      |
                        +--+fn        |                      |
                        |             |                      |
                        | +-------------------------+        |
                        | +---+name   |             |        |
                        | |           |             |        |
                        | +---+sex    |             |        |
                        | |           |             |        |
                        | +---+fn2   <+-----+       |        |
                        | |                 |       |        |
                        | |   +---------------+     |        |
                        | |   |             | |     |        |
                        | |   +----+name    | |     |        |
                        | |   |          +--+ |     |        |
                        | |   +----+age       |     |        |
                        | |   |               |     |        |
                        | |   +---------------+     |        |
                        | +-------------------------+        |
                        |                                    |
                        +------------------------------------+
```

> 当当前作用域没有需要访问的变量时，便会沿着作用域链一级一级往上查找，直到全局作用域为止。所以，越里层的同名变量优先级越高。

**代价**：

当作用域链很长时，查询会越来越长，影响程序性能。局部变量访问速度优于全局变量，内层变量访问速度优于外层变量访问速度。

> jQuery 源码体现：
>
> 将 window 对象作为参数传递到函数中，而不是直接使用？
>
> 答：将 window 对象传递到函数中， window 对象就变成局部变量，访问速度会加快。另一原因，传入到函    数可以使用 w, $ 等比较简短的变量名来代替，方便代码压缩。

**延长作用域链**

with 在作用域链顶端创建一个作用域。

```javascript
var person = {};
person.name = 'xm';
person.sex = 'male';
var score = 4;

with(person){
    name = 'xh';
    sex = 'female';
    score = 44;
}

console.log(person.name);	//=> xh
console.log(person.sex);	//=> female
console.log(score);			//=> 44
```

> 上述代码，首先会在 window 全局作用域上添加一个局部作用域， 变量对象为 person。
>
> 此时，全局作用域上，变量对象为 window，它包含有 score 变量和 person 作用域。新增的 person 作用域下没有变量和属性，变量对象为 person.
>
> 当程序开始执行到第 7 行时，由于局部作用域上的变量和方法都是属于变量对象的，所以程序会沿着作用域链查找 person.name 并赋值，sex 变量同理。而 person.score 没找到，则对同名变量 score 进行赋值。
>
> 这里的理解：应该是查找到同名变量即可。

### JS解析机制

JS的解析过程分为两步：

1. 预解析
2. 逐行解读代码

```javascript
var name = 'xm';
var age = 18;
function fn(argument){
    console.log(name);
    var name = 'xh';
    var age = 10;
}
fn();	//=>undefined
```

 变量对象window 包含 name、age、fn。变量对象fn 包含 name、age、argument。

1. 预解析过程，在两个作用域分别进行预解析。

   * 全局作用域下的预解析

   ​	在 window 作用域下查找 **所有 var 声明的变量**，并赋值为 undefined. 

   ​	接着，查找所有 **function** 声明.

    ```javascript
    window
        name = undefined
        age = undefined
        function fn(argument){
            console.log(name);
            var name = 'xh';
            var age = 10;
        }
    ```
   
   * fn 局部作用域的预解析

    ```javascript
    fn
        name = undefined
        age = undefined
        argument = undefined
    ```

2. 逐行解读

   * line 1~2： name = 'xm'，age = 18.

   * line 3~7：预解析时已经完成，跳过。
   * line 8：执行 fn()。根据 fn 局部作用域内的解析结果，name 为 undefined，所以结果输出 undefined。

**预解析变量与函数同名时的规则**：

> 1. 同名变量出现多次，值依旧是 undefined。
> 2. 变量与函数同名，保留函数。
> 3. 函数同名，后定义的优先(覆盖)。

老版本浏览器中，在 if-else 组成的代码块中声明函数，会造成预解析错误。

```javascript
if(){
   var i = 0;
   function fn1(argument){}
}

for(){
    var j = 0;
    function fn2(argument){}
}
```

### JS预解析测试

**一**

```javascript
<script>
	console.log(a);		
	var a = 1;
</script>
```

> 结果：undefined

```javascript
<script>
	console.log(a);		
	a = 1;
</script>
```

> 结果：报错，ReferenceError: a is not defined。
>
> JS 预解析只对有 var 声明的变量进行预解析，所以 a 变量没有被解析，程序执行时，找不到 a , 所以执行时出现错误。ps：未使用 var 声明的变量都是属于全局变量对象 window 上的变量。

**二**

```javascript
<script>
    console.log(a);
    var a = 1;
    function a(){
        console.log(2);
    }
    console.log(a);
    var a = 3;
    console.log(a);
    function a(){
        console.log(4);
    }
    console.log(a);
    a();
</script>
```

> 结果：
>
> ƒ a(){
>     console.log(4);
> }
>
> 1
>
> 3
>
> 3
>
> 报错：Uncaught TypeError: a is not a function

解析过程如下：

> line 2 :  a = undefined
>
> line 3~5：变量名与函数同名，保存函数。
>
> ​				function a(){	console.log(2);	}
>
> line 7 ： 函数优先。
>
> line 9 ：后定义的函数优先，最终预解析结果如下：
>
> ​				function a(){	console.log(4);	};

**三**

```javascript
<script>
	console.log(a);
</script>
<script>
    var a = 1;
</script>
```

> 结果：报错，ReferenceError: a is not defined。预解析时分标签独立运行，没有联系。

```javascript
<script>
    var a = 1;
</script>
<script>
    console.log(a);
</script>
```

> 结果：1。
>
> 前一 script 标签预解析并执行后，a 是全局作用域上的变量，值为1。后一 script 标签读取和使用 a 的值不会出现错误。

**四**

```javascript
<script>
    var a = 1;
    function fn(){
        console.log(a);
        var a = 2;
    }
    fn();
    console.log(a);
</script>
```

> 结果：undefined 1;

预解析过程：

> 全局作用域预解析：a = undefined, function fn(){	xxx	}
>
> fn 局部作用域预解析：a = undefined

逐行代码执行过程：

> line 1：全局作用域上：a = 1
>
> line 6：fn 局部作用域：预解析后的 a 仍为 undefined,输出 undefined.
>
> line 7：输出全局作用域上的 a 的值.

**五**

```javascript
<script>
    var a = 1;
    function fn(){
        console.log(a);
        a = 2;
    }
    fn();
    console.log(a);
</script>
```

> 结果： 1 2

预解析过程：

> 全局作用域：a = undefined
>
> fn 局部作用域：没有需要预解析。

逐行代码执行过程：

> line 1: 全局作用域上 a = 1;
>
> line 6: fn 函数中，没有预解析，沿着作用域链往外查找 a，找到全局作用上的变量 a，输出。
>
> line 4: 没有使用 var 声明的变量是全局作用域上的变量，这里的 a 也就是外层的 a, a 的值变为 2.
>
> line 7: 输出的是全局变量上 a 的值2.

**六**

```javascript
<script>
    var a = 1;
    function fn(a){
        console.log(a);
        a = 2;
    }
    fn();
    console.log(a);
</script>
```

> 结果： undefined 1;

预解析过程：

> 全局作用域： a = undefined
>
> fn 局部作用域：a 是参数，当做函数局部变量看待， a = undefined;

逐行代码执行过程：

> line 1: 全局作用域上 a = undefined
>
> line 6: 输出预解析时的值  undefined
>
> line 4: 对 fn 函数参数 a 进行赋值，值为 2.
>
> line 7: 输出全局作用域上的 a。

**七**

```javascript
<script>
    var a = 1;
    function fn(a){
        console.log(a);
        a = 2;
    }
    fn(a);
    console.log(a);
</script>
```

> 结果： 1 1.

预解析过程：

> 全局作用域：a = undefined。
>
> fn 局部作用域：fn 函数中 a 参数作为局部变量， a = undefined

逐行代码执行过程：

> line 1：全局作用域上的 a 被赋值为 1。
>
> line 6：fn 函数被调用， a 的值作为参数传递到 fn 函数中， 等价于执行了 a = 1。
>
> line 3：输出 fn 内局部变量 a 的值。
>
> line 4：对局部变量 a 的赋值为2。
>
> line 7：输出全局作用域上的变量 a 的值。

### 垃圾收集机制

垃圾收集机制：释放无用的数据，回收内存。两种方式：自动收集和手动收集。

原理：垃圾收集器按照固定的事件间隔去找出内存中不再继续使用的变量，打上标记，释放其占用的内存。

**标识无用数据的策略**

* 标记清除

  先给所有变量打上标记，再将环境中的变量的标记去除。剩下的是已离开环境的变量，将被回收。

* 引用计数

  引用次数为 0 的变量将会被回收。

  ```javascript
  var xm = {
      name: 'xm',
      age: 18
  };
  var xh = xm;	//=> 引用次数为2
  xh = {};		//=> 引用次数减1，为1
  xm = {};		//=> xm 原来指向的内存空间的引用次数减为1，为0，这片内存空间将会被回收
  ```

**循环引用**

循环引用的问题在一些使用引用计数策略的老版本浏览器上会出现。

```javascript
function fn(argument){
    var xm = {};	//=>引用计数为1
    var xh = {};	//=>引用计数为1
    xm.wife = xh;	//=>引用计数为2
    xh.husband = xm;//=>引用计数为2
}
fn();
```

> 当 line 7 执行完后，xm = null，此时引用计数为1，xh = null，引用计数也为1。这个函数无论调用多少次，xm 与 xh 的引用计数永远为 1，所以不会被回收。
>
> 解决方法：可以将对象所有的属性值置为 null，手动解除引用。如：
>
> xm.wife = null;
>
> xh.husband = null;
>
> xm = null;
>
> xh = null;