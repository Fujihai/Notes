# ECMAScript 与 JavaScript

ES 是规范，是  JS 的标准。

JS 是 ES 的实现。

# let 与块级作用域

## let 与 var 的主要区别

* let 声明的变量只在当前(块级)作用域有效
* let 声明的变量不能被重复声明
* 不存在变量提升

## ES6 之前的作用域

* 全局作用域
* 函数作用域
* eval 作用域

## 块级作用域

在 ES6 中，一对花括号中 `{ }` 的区域就是块级作用域。

* if () {}
* switch () {}
* for () {}
* try {} catch (err) {}
* {}

> 对象声明中的花括号除外。

### 块级作用域可以嵌套

```javascript
{
    // 块级作用域 1 
    {
        // 块级作用域 2
    }
}
```

### 实例

#### let 只作用在当前块级作用域。

```javascript
{
    var a = 1;
    let b = 2;
}
console.log(a); //=> 1
console.log(b); //=> b is no defined
```

> line 5 : var 声明的变量不受块级作用域影响。
>
> line 6 : let 声明的变量只作用在当前块级作用域，作用域外无法正常访问。

```javascript
{
    let a = 1;
    {
        console.log(a);	//=>1
        let b = 2;
    }
    console.log(b);	//=> b is not defined
}
```

> line 4 : 内层花括号形成的块级作用域与 a 都属于外层的块级作用域，所以可以访问 a。
>
> line 7 : b 只属于内层的块级作用域，所以无法直接访问。

```javascript
if (true) {
    var a = 1;
    let b = 2;
}

console.log(a);
console.log(b);
```

> if 语句的花括号形成一个块级作用域。

```javascript
for (let i = 0; i < 3; i++) {
    console.log(i); //=> 依次输出 0， 1， 2， 3
}
console.log(i);	//=>i is not defined
```

> for 语句会形成两个块级作用域，一个是 **圆括号内的块级作用域** ， 一个是 **花括号内的块级作用域**。
>
> 圆括号内的块级作用域是在外层，花括号的块级作用域在里层。类似下面的结构。
>
> {
>
> ​	let i = 0;
>
> ​	{
>
> ​		console.log(i)；
>
> ​	}
>
> }

```javascript
{
    var a = 1;
    {
        console.log(a);	//=>1
        var b = 2;
    }
    console.log(b);	//=> 2
}
```

> 使用 var 声明的变量不受块级作用域影响，在预解析时均未 undefined。依次往下执行时被赋值，正常输出。

#### 使用 let 或者 const 声明的变量，不能被重新声明

```javascript
var dad = "我是爸爸";
console.log(dad);	//=> 我是爸爸
var dad;
console.log(dad);	//=> 我是爸爸
var dad = "我才是爸爸";
console.log(data);	//=> 我才是爸爸
```

> line 4：使用 var 声明的变量重新声明后，依旧保留原来的值。
>
> line 5：修改 var 声明的变量的值，新值覆盖旧值。

```javascript
let son = "我是儿子";
console.log(son);	//=> 我是儿子
let son;
console.log(son);	//=> 报错
let son = "我才是儿子";
console.log(son);	//=> 报错
```

> line 4 && 6 : Uncaught SyntaxError: Identifier 'son' has already been declared.
>
> 使用 let 声明的变量不能被重新声明。

#### let 不存在变量提升

变量提升也就是预解析过程。

```javascript
console.log(dad);	//=> 我是爸爸
var dad = "我是爸爸";

console.log(son);   //=> Uncaught ReferenceError: Cannot access 'son' before initialization
let son = "我是儿子";
```

> let 不存在变量提升，不会被预解析，当程序往下执行后，找不到 son 的定义，报错。

#### 暂存死区

```javascript
let monkey = '我是美猴王';
{
    console.log(monkey);
    let monkey = '我不是美猴王';
}
console.log(monkey);
```

> line 3 :  Uncaught ReferenceError: Cannot access 'monkey' before initialization.
>
> line 2 - 5 : 这两行之间形成了一个块级作用域，首先，由于 let 不存在变量提升，所以，line 3 的输出无法访   				  问 line 4 的定义。在 ES6 中，块级作用域内使用 let , const 声明的变量，即使作用域链外层有同名				  的变量，也无法访问。故报错。

#### 面试小例子

q：生成十个按钮，每个按钮点击的时候弹出 1 - 10.

##### 方案一

```javascript
var i = 0;
for(i = 1; i <= 10; i++) {
    (function(i){
        var btn = document.createElement('button');
        btn.innerText = i;
        btn.onclick = function() {
            alert(i);
        };
        document.body.appendChild(btn);
    })(i);
}
```

##### 方案二u

```javascript
for(let i = 1; i <= 10; i++) {
    var btn = document.createElement('button');
    btn.innerText = i;
    btn.onclick = function() {
        alert(i);
    };
    document.body.appendChild(btn);
}
```

# const

常量：不可改变的量，必须在声明的时候赋值。

否则报错： Missing initializer in const declaration。

## 与 let 类似的特性

* 不能重复声明
* 不存在提升
* 只在当前(块级)作用域内有效

## 常量不可变

* 一旦声明为常量，就不能再改变
* 常量为引用类型的时候，不能保证不可变

## 实例

### 常量被赋值后不能再修改

```javascript
const a = 1;
a = 2;
```

> line 2 : Uncaught TypeError: Assignment to constant variable.

### 常量为引用类型的时候 可以修改该引用类型

```javascript
const xiaoming = {
    age: 14,
    name: '小明'
};
console.log(xiaoming);	//=>{age: 14, name: "小明"}
xiaoming.age = 22;
console.log(xiaoming);	//=>{age: 22, name: "小明"}
xiaoming = {};			//=> Uncaught SyntaxError: Identifier 'xiaoming' has already been declared
```

> line 8 : const 只能保证常量指向的地址值不变，不能保证指向的内容不变。
>
> 针对于引用类型，const 保证引用不变，不能保证引用指向的地址内容不变。

## 关于 const 的一些问题

- q ：怎么防止常量为引用类型的时候能被修改的情况

```javascript
const xiaoming = {
    age: 14,
    name: '小明'
};
Object.freeze(xiaoming);
console.log(xiaoming);	//=>{age: 14, name: "小明"}
xiaoming.age = 22;
xiaoming.dd = 66;
console.log(xiaoming);	//=>{age: 14, name: "小明"}
```

> 使用 Object.freeze() 方法，参数是引用类型变量「Object，Array，function?」。

- q  :  ES6 之前怎么声明常量

使用 `Object.defineProperty()` ，设置对象某个属性不可以被修改。

```javascript
var CST = {};

Object.defineProperty(CST, 'BASE_NAME', {
    value: '小明',
    writeable: false
});
```

> line 3 : 表示往 CST 对象上挂载属性 `BASE_NAME` ，值为 `小明` ， 不可写。
>
> 若不是挂载在某个对象上的话，可以将第一个参数设置为 window 全局对象。

使用 `Object.seal(CST)` ，表示对象不可以扩展，即不可以添加属性。

### 封装一个 freeze 方法

思路

1. 遍历属性和方法
2. 使用 `Object.defineProperty` 设置每个属性不可写
3. Object.seal() 将目标对象设置为不可扩展

```javascript
Object.defineProperty(Object, 'freezePolyfill', {
    value: function(obj) {
        var i;
        for(i in obj) {
            if(obj.hasOwnProperty(i)) {
                Object.defineProperty(obj, i, {
                    writable: false
                });
            }
        }
        Object.seal(obj);
    }
});

//测试
const xiaoming = {
    age: 14,
    name: '小明'
    obj: {
      a : 1
   }
};
Object.freezePolyfill(xiaoming);
```

> line 18 & 20：若属性是对象，上述实现无法冻结该属性，需要使用递归的方式来实现。

递归版实现思路

```javascript
Object.defineProperty(Object, "freezePolyfill",{
		value: function(obj){
			var i;
			// outerProp为属性名，类型为string
			for(i in obj){
				if(typeof obj[i] == 'object' && obj[i] !== null){
					Object.freezePolyfill(obj[i]);
				}else if(obj.hasOwnProperty(i)){
					Object.defineProperty(obj, i, {
						writable: false
					});
				}
			}
			Object.seal(obj);
		}
	});

const xiaohai = {
    age: 22,
    name: 'john',
    habits: {
        sport: "Basketball",
        sleep: 12
    }
};
```

### hasOwnProperty

```javascript
var obj1 = {
    a: 1,
    b: 2
}

var obj2 = Object.create(obj1);

obj2.c = 3;
obj2.d = 4;

for (let i in obj2) {
    //console.log(i + ': ' + obj2[i]);	//c: 3 d: 4 a: 1 b: 2
    if(obj2.hasOwnProperty(i)) {
        console.log(i + ': ' + obj2[i]);	//c: 3 d: 4
    }
}
```

> line 12 : 使用 for 循环，会遍历到原型上的属性。使用 `hasOwnProperty` 可以判断属性是否是自身上的属性。