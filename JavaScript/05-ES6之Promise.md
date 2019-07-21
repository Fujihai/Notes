# 什么是 Promise

Promise 对象用于表示一个异步操作的最终状态（完成或失败）以及其返回的值。

Promise 中处理的是未来要做的事情，成功的时候就会做成功的事情，失败的时候就会做失败的事情。

# 同步 & 异步

同步任务会阻塞程序执行(alert、for、.....)

异步任务不会阻塞程序执行(setTimeout、fs.readFile、....)

# Promise 主要方法

* Promise(then、catch、finally)
* Promise.all & Promise.race
* Promise.resolve & Promise.reject

# 回调与 Promise

## 回调地狱

```javascript
function f(cb) {
    setTimeout(function() {
        cb && cb();
    }, 1000);
}

f(function() {
    console.log(1);
    
    f(function(){
        console.log(2);
        
        f(function(){
            console.log(3);
            
            f(function() {
                console.log(4);
                
                f(function() {
                    console.log(5);
                });
            });
        });
    });
});
```

> line 3 : 判断函数是否为 null，不为 null 就执行。
>
> 函数 1 秒后执行回调函数，并输出 1，之后每隔 1 秒后，依次输出 2,3,4,5。当后续执行依赖于上一次的执行后，业务逻辑复杂后，使用回调函数会导致代码臃肿并难以维护。

## 使用 Promise 解决回调地狱

```javascript
function f() {
    return new Promise(resolve => {
        setTimeout(function() {
            resolve();
        }, 1000);
    });
}

f()
    .then(function(){
    console.log(1);
    return f();
})
    .then(function(){
    console.log(2);
    return f();
})
    .then(function(){
    console.log(3);
    return f();
})
    .then(function(){
    console.log(4);
    return f();
})
    .then(function(){
    console.log(5);
});
```

> Promise 是一个对象，它的实例包含有 then 方法，.then 表示接下来要做的事。
>
> line 1 ~ 7 :  函数的定义，转换成 ES5 语法，如下
>
> ​					function f() {
>   						return new Promise(function (resolve) {
> ​    								setTimeout(function () {
> ​     								resolve();
> ​    							}, 1000);
>  					 	});
> ​					}
>
> resolve 就是 then 中的函数。在此例中，每 1 秒就会执行 then 中传递进来的回调函数。在回调函数中返回原来的 Promise 实例，若不返回，.then 依旧执行。

**[在线 ES6 转 ES5](<https://babeljs.io/repl/#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=GYCglAUABDswdAFwBYFMB2JxQLwD4oBvaOUgYwHt0BnCgG1XjooHMQBGMAbhNNgCdUiAK790UUN16kAvpD4IUGLGFwFiC2JRr1GzNgCYpmmIJFiJ4HprnS4SNJmz4id8lVoMmrEAGZjJmai4pLWCraaDsrO6m5w2p56PgAsAZpBFqFxUBEKUU6qLhomCbrebACsabLcQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=true&fileSize=false&timeTravel=false&sourceType=module&lineWrap=false&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.5.5&externalPlugins=>)**

> then 中的回调函数可以使用 **箭头函数** 方式来进行简化：.then(() => {  });
>
> 花括号即回调函数的函数体，也就是要执行的内容。

# 回调与 Promise 的流程控制对比

## 回调方式

```javascript
function moveTo(el, x, y, cb) {
    el.style.transform = `translate(${x}px, ${y}px)`;
    setTimeout(function() {
        cb && cb();
    },1000);
}

let el = document.querySelector('div');

document.querySelector('button').addEventListener('click', e => {
    moveTo(el, 100, 100, function() {
        moveTo(el, 200, 200, function() {
            moveTo(el, 100, 300, function() {
                moveTo(el, 130, 20, function() {
                    moveTo(el, 0, 0, function() {
                        console.log('移动结束');
                    });
                })
            })
        })
    });
});
```

## Promise 方式

```javascript
function moveTo(el, x, y) {
    el.style.transform = `translate(${x}px, ${y}px)`;

    return new Promise(resolve => {
        setTimeout(function() {
            resolve();
        }, 1000);
    });
}

let el = document.querySelector('div');

document.querySelector('button').addEventListener('click', e => {
    moveTo(el, 100, 100)
        .then(() => {
        return moveTo(el, 200, 200);
    })
        .then(() => {
        return moveTo(el, 100, 300);
    })
        .then(() => {
        return moveTo(el, 130, 20);
    })
        .then(() => {
        moveTo(el, 0, 0);
    });
});
```

## 总结

对比两种方式发现，Promise 代码结构更加清晰易懂，也便于维护。

# Promise 错误处理

```javascript
function f(val) {
    return new Promise((resolve, reject) => {
        if(val) {
            resolve({ name: '小明' }, 1000);
        } else {
            reject('404');
        }
    });
}

f(true)
    .then((data, data2) => {
    console.log(data);
    console.log(data2);
}, e => {
    console.log(e);		//=> 404
});
```

> line 6 : reject 中的是当 Promise 执行 reject 时传递的参数，在 line 15 中，通过 e 来获取。
>
> line 14 : 使用 resolve 只能传递一个参数。因为回调函数中只能获取到第一个参数，第二个参数无法获取。

```javascript
function f(val) {
    return new Promise((resolve, reject) => {
        if(val) {
            resolve({name: '小明'});
        }else {
            reject('404');
        }
    });
}

f(true)
    .then(data => {
    console.log(data);
    return f(false);
})
    .then(() => {
    console.log('我永远不会被输出！');
})
    .then(() => {

}, e => {
    console.log(e + ' 被处理！');
})
    .catch(e => {
    console.log(e);
    return f(false);
}).finally(() => {
    console.log("一定会执行！");
});
```

> 结果：
>
> line 13 : { name : '小明' }
>
> line 22 : 404 被处理！
>
> line 25 :  undefined
>
> line 28 ：一定会执行！

说明：

* line 14：返回一个 reject 状态的 promise 实例。

* line 16：由于需要处理的是错误，传入只有 resolve 回调函数 ，所以不会输出内容。

* line 22：处理了 reject 错误，所以会被输出。

* line 25：由于上一个操作已经处理了 reject 错误，并且没有任何返回，捕获不到错误，输出 				       	        undefined。

* line 26：在最后的 catch 中抛出错误无法被处理，报错信息 ： Uncaught (in promise) 404。
* line 28:   finally块的内容一定会被执行。

# Promise 的状态

## 三种状态

* pending - 进行中
* fulfilled - 成功
* rejected - 失败

## 状态改变

Promise 的状态改变也称 **决议** 。

状态的改变 **不可逆**，一旦决议就 **不能再修改**。

**状态的变化只能从 pending 变成 fulfilled 或 rejected。**

# Promise.all

Promise.all 方法可以把多个 promise 实例，包装成一个新的 promise 实例

```javascript
Promise.all([ promise1, promise2 ]) : Promise
```

> Promise.all 接收一个 promise 组成的数组，返回一个新的 promise 实例。
>
> 分三种情况：
>
> * promise 参数数组中的 promise 全部决议为成功，则将参数数组中每个 promise 调用 resolve 方法时传递的参数组合成一个数组，且数组中的参数顺序与 promise 的参数一一对应。
> * 当 promise 参数数组中的任一 promise 调用的是 reject 方法，则 promise.all 决议失败，并将调用 reject 的参数传递给我们。
> * 若 promise.all 参数数组为空，则决议为成功，

#### 使用 promise 模拟数据获取

```javascript
function getData1() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('第一条数据加载成功');
            resolve('data1');
        }, 1000);
    });
}

function getData2() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('第二条数据加载成功');
            resolve('data2');
        }, 1000);
    });
}

function getData3() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('第三条数据加载成功');
            resolve('data3');
        }, 1000);
    });
}

function getData4() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('第四条数据加载成功');
            reject('data4');
        }, 1000);
    });
}
```

> 前三个 promise 为 resolve，后一个为 reject。

#### promise 参数数组全为 resolve

```javascript
let p = Promise.all([getData1(), getData2(), getData3()]);

p.then(arr => {
    console.log(arr); //=> ['data1', 'data2', 'data3', 'data4']
});
```

> line 28 : 数组中的 promise 实例全部调用 resolve 方法，promise.all 决议成功。
>
> line 30 : 返回的 promise.all 实例中，获取每个 promise 调用 resolve 方法时传递的参数组成的数组。

#### promise 参数数组有 reject

```javascript
let p = Promise.all([getData1(), getData2(), getData3(),getData4()]);

p.then(arr => {
    console.log(arr);
}, e => {
    console.log(e);	//=> data4
});
```

> 结果输出调用了 promise.reject 时传递的参数。

# Promise.race

Promise.race 方法与 Promise.all 方法参数是相似的。当数组中某个 promise 调用了 resolve 或者 reject，则整个 Promise.race 也调用与该 promise 相同的方法。即参数中某个 promise 决议了，则 Promise.race 也决议了。

**若数组为空，则不决议。**

```javascript
let p = Promise.race([getData1(), getData2(), getData3()]);

p.then(arr => {
    console.log(arr);   //=>data1
}, e => {
    console.log(e);	
});

let p2 = Promise.race([getData4(), getData1(), getData2()]);

p.then(arr => {
    console.log(arr);
}, e => {
    console.log(e);		//=>data4
});

```

> line 1 ： getData1() 先决议，其调用 Promise.resolve() 方法，Promise.race 也决议为 resolve ，输出 getData1 中 Promise.reslove() 传递的参数 data1。
>
> line 9 ： getData4() 先决议，同上。

# Promise.resolve 与 Promise

## Promise.resolve()

resolve 方法接收参数时有 3 种情况：

1. 传递一个普通的值

   ```javascript
   let p1 = new Promise(resolve => {
       resolve('成功！');
   });
   
   let p2 = Promise.resolve('成功！');
   ```

   > Promise.resolve() 被调用时会返回一个 Promise 对象， 以上两种方式等价。

2. 传递一个 promise 实例

   ```javascript
   let prom = new Promise(resolve => {
       resolve('耶！');
   });
   
   let p = Promise.resolve(prom);
   
   p.then(data => void console.log(data));  //=> '耶！'
   
   console.log(p === prom);			//=> true
   ```

   > line 5 : Promise.resolve() 直接将传入的 prom 对象返回并赋值给了 p。

3. 传递一个 thenable ，鸭子类型？

   ```javascript
   let obj = {
       then(cb) {
           console.log('我被执行了');
           cb('哼！');
       },
       oth() {
           console.log('我被抛弃了');
       }
   }
   
   Promise.resolve(obj).then(data => {
       console.log(data);		//=> 哼！
   });
   ```

   > obj 是一个具有 then 方法的对象。
   >
   > 结果： 我被执行了
   >
   > ​			哼！
   >
   >  obj 对象作为参数传递进 resolve 方法中，obj.then 就会被执行，then 中的内容作为回调函数传递进    		then 中， line 3 调用时会将 **哼** 传递给回调函数，最后，正常输出。

## Promise.all 与 Promise.race

Promise.all 与 Promise.race 的参数是数组，若数组中的不是 promise 实例，则 Promise 会将其包装，转换成 Promise 实例再使用。

使用和设计类库时，可以使用 Promise.resolve 将参数变成 Promise 对象。

## Promise.reject

```javascript
Promise.reject({ then() { console.log(1) } })
		.then(() => {
    	console.log('我不会被执行');
}, e => {
    console.log(e);		//=> {then() {console.log(1)}}
});
```

> reject 方法不会将参数包装成 Promise 实例，而是直接返回。

## 小例子

```javascript
console.log(1);
      
let p = new Promise(resolve => {
    console.log(2);
    resolve();
    console.log(3);
});

console.log(4);

p.then(() => {
    console.log(5);
});

console.log(6);
```

> 结果：1 2 3 4 6 5
>
> 原因：异步操作是在所有同步操作后执行。

## 同步任务转成异步任务

```javascript
//同步任务变成异步任务
function createAsyncTask(syncTask) {
    return Promise.resolve(syncTask).then(syncTask => syncTask());
}

createAsyncTask(() => {
    console.log('我变成了异步任务！！！');
    return 1 + 1;
}).then(res => {
    console.log(res);
});

console.log('我是同步任务!');
```

> 结果：
>
> ​			我是同步任务！
>
> ​			我变成了异步任务！！！
>
> ​			2