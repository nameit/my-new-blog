title: promise
tags: [promise,js]
categories: 前端
---


# 一 前言

一直对promise一知半解，当阅读到[其他文章](https://segmentfault.com/a/1190000007032448#articleHeader16)时对它有个明确的认识。

# 二 什么是promise

以下是MDN对Promise的定义

>The Promise object is used for asynchronous computations. A Promise represents a single asynchronous operation that hasn't completed yet, but is expected in the future.
译文：Promise对象用于异步操作，它表示一个尚未完成且预计在未来完成的异步操作。

那么什么是异步操作？在学习promise之前需要把这个概念搞明白，下面将抽离一章专门介绍。

## 同步与异步

我们知道，JavaScript的执行环境是「单线程」。 
所谓单线程，是指JS引擎中负责解释和执行JavaScript代码的线程只有一个，也就是一次只能完成一项任务，这个任务执行完后才能执行下一个，它会「阻塞」其他任务。这个任务可称为主线程。 
但实际上还有其他线程，如事件触发线程、ajax请求线程等。

这也就引发了同步和异步的问题。

### 同步

同步模式，即上述所说的单线程模式，**一次**只能执行**一个**任务，函数调用后需等到函数执行结束，返回执行的结果，才能进行下一个任务。如果这个任务执行的时间较长，就会导致「**线程阻塞**」。

```
/* 例2.1 */
var x = true;
while(x);
console.log("don't carry out");    //不会执行
```

上面的例子即同步模式，其中的while是一个死循环，它会阻塞进程，因此第三句console不会执行。 
同步模式比较简单，也较容易编写。但问题也显而易见，如果请求的时间较长，而阻塞了后面代码的执行，体验是很不好的。因此对于一些耗时的操作，异步模式则是更好的选择。

### 异步

下面就来看看异步模式。 
异步模式，即与同步模式相反，可以一起执行**多个任务**，函数调用后不会立即返回执行的结果，如果任务A需要等待，可先执行任务B，等到任务A结果返回后再继续回调。 
最常见的异步模式就数定时器了，我们来看看以下的例子。

```
/* 例2.2 */
setTimeout(function() {
    console.log('taskA, asynchronous');
}, 0);
console.log('taskB, synchronize');
//while(true);

-------ouput-------
taskB, synchronize
taskA, asynchronous
```

我们可以看到，定时器延时的时间明明为0，但taskA还是晚于taskB执行。这是为什么呢？由于定时器是异步的，**异步任务会在当前脚本的所有同步任务执行完才会执行**。如果同步代码中含有死循环，即将上例的注释去掉，那么这个异步任务就不会执行，因为同步任务阻塞了进程。

### 回调函数

提起异步，就不得不谈谈回调函数了。上例中，setTimeout里的function便是回调函数。可以简单理解为：（执行完）回（来）调（用）的函数。
以下是WikiPedia对于callback的定义。

>In computer programming, a callback is a piece of executable code that is passed as an argument to other code, which is expected to call back (execute) the argument at some convenient time.

可以看出，回调函数是一段可执行的代码段，它以「参数」的形式传递给其他代码，在其合适的时间执行这段（回调函数）的代码。

WikiPedia同时提到

>The invocation may be immediate as in a synchronous callback, or it might happen at a later time as in an asynchronous callback.

也就是说，回调函数不仅可以用于异步调用，一般同步的场景也可以用回调。在同步调用下，回调函数一般是最后执行的。而异步调用下，可能一段时间后执行或不执行（未达到执行的条件）。

```
/* 例2.3 */
/******************同步回调******************/
var fun1 = function(callback) {
    //do something
    console.log("before callback");
    (callback && typeof(callback) === 'function') && callback();
    console.log("after callback");
}
var fun2 = function(param) {
    //do something
    var start = new Date();
    while((new Date() - start) < 3000) { //delay 3s
    }
    console.log("I'm callback");
}
fun1(fun2);

-------output--------
before callback
//after 3s
I’m callback
after callback
```
由于是同步回调，会阻塞后面的代码，如果fun2是个死循环，后面的代码就不执行了。

上一小节中setTimeout就是常见的异步回调，另外常见的异步回调即ajax请求。

```
/* 例2.4 */
/******************异步回调******************/
function request(url, param, successFun, errorFun) {
    $.ajax({
        type: 'GET',
        url: url,
        param: param,
        async: true,    //默认为true,即异步请求；false为同步请求
        success: successFun,
        error: errorFun
    });
}
request('test.html', '', function(data) {
    //请求成功后的回调函数，通常是对请求回来的数据进行处理
    console.log('请求成功啦, 这是返回的数据:', data);
},function(error) {
    console.log('sorry, 请求失败了, 这是失败信息:', error);
});
```

## 为什么使用Promise

说完了以上基本概念，我们就可以继续学习Promise了。
上面提到，Promise对象是用于异步操作的。既然我们可以使用异步回调来进行异步操作，为什么还要引入一个Promise新概念，还要花时间学习它呢？不要着急，下面就来谈谈Promise的过人之处。
我们先看看下面的demo，利用Promise改写例2.4的异步回调。

```
/* 例2.5 */
function sendRequest(url, param) {
    return new Promise(function (resolve, reject) {
        request(url, param, resolve, reject);
    });
}

sendRequest('test.html', '').then(function(data) {
    //异步操作成功后的回调
    console.log('请求成功啦, 这是返回的数据:', data);
}, function(error) {
    //异步操作失败后的回调
    console.log('sorry, 请求失败了, 这是失败信息:', error);
});
```
这么一看，并没有什么区别，还比上面的异步回调复杂，得先新建Promise再定义其回调。其实，Promise的真正强大之处在于它的多重链式调用，可以避免层层嵌套回调。如果我们在第一次ajax请求后，还要用它返回的结果再次请求呢？

```
/* 例2.6 */
request('test1.html', '', function(data1) {
    console.log('第一次请求成功, 这是返回的数据:', data1);
    request('test2.html', data1, function (data2) {
        console.log('第二次请求成功, 这是返回的数据:', data2);
        request('test3.html', data2, function (data3) {
            console.log('第三次请求成功, 这是返回的数据:', data3);
            //request... 继续请求
        }, function(error3) {
            console.log('第三次请求失败, 这是失败信息:', error3);
        });
    }, function(error2) {
        console.log('第二次请求失败, 这是失败信息:', error2);
    });
}, function(error1) {
    console.log('第一次请求失败, 这是失败信息:', error1);
});
```

以上出现了多层回调嵌套，有种晕头转向的感觉。这也就是我们常说的厄运回调金字塔（Pyramid of Doom），编程体验十分不好。而使用Promise，我们就可以利用then进行「链式回调」，将异步操作以同步操作的流程表示出来。

```
/* 例2.7 */
sendRequest('test1.html', '').then(function(data1) {
    console.log('第一次请求成功, 这是返回的数据:', data1);
}).then(function(data2) {
    console.log('第二次请求成功, 这是返回的数据:', data2);
}).then(function(data3) {
    console.log('第三次请求成功, 这是返回的数据:', data3);
}).catch(function(error) {
    //用catch捕捉前面的错误
    console.log('sorry, 请求失败了, 这是失败信息:', error);
});
```
是不是明显清晰很多？孰优孰略也无需多说了吧~下面就让我们真正进入Promise的学习。

# 三 Promise的基本用法

## 基本用法

上一小节我们认识了promise长什么样，但对它用到的resolve、reject、then、catch想必还不理解。下面我们一步步学习。

Promise对象代表一个未完成、但预计将来会完成的操作。
它有以下三种状态：

- pending：初始值，不是fulfilled，也不是rejected
- fulfilled：代表操作成功
- rejected：代表操作失败

Promise有两种状态改变的方式，既可以从pending转变为fulfilled，也可以从pending转变为rejected。一旦状态改变，就「凝固」了，会一直保持这个状态，不会再发生变化。当状态发生变化，promise.then绑定的函数就会被调用。
注意：Promise一旦新建就会「立即执行」，无法取消。这也是它的缺点之一。
下面就通过例子进一步讲解。

```
/* 例3.1 */
//构建Promise
var promise = new Promise(function (resolve, reject) {
    if (/* 异步操作成功 */) {
        resolve(data);
    } else {
        /* 异步操作失败 */
        reject(error);
    }
});
```

类似构建对象，我们使用new来构建一个Promise。Promise接受一个「函数」作为参数，该函数的两个参数分别是resolve和reject。这两个函数就是就是「回调函数」，由JavaScript引擎提供。

resolve函数的作用：在异步操作成功时调用，并将异步操作的结果，作为参数传递出去； 
reject函数的作用：在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

Promise实例生成以后，可以用then方法指定resolved状态和reject状态的回调函数。

```
/* 接例3.1 */
promise.then(onFulfilled, onRejected);

promise.then(function(data) {
  // do something when success
}, function(error) {
  // do something when failure
});
```

then方法会返回一个Promise。它有两个参数，分别为Promise从pending变为fulfilled和rejected时的回调函数（第二个参数非必选）。这两个函数都**接受Promise对象传出的值作为参数**。
简单来说，then就是定义resolve和reject函数的，其resolve参数相当于：

```
function resolveFun(data) {
    //data为promise传出的值
}
```

而新建Promise中的'resolve(data)'，则相当于执行resolveFun函数。
Promise新建后就会立即执行。而then方法中指定的回调函数，将在当前脚本所有同步任务执行完才会执行。如下例：

```
/* 例3.2 */
var promise = new Promise(function(resolve, reject) {
  console.log('before resolved');
  resolve();
  console.log('after resolved');
});

promise.then(function() {
  console.log('resolved');
});

console.log('outer');

-------output-------
before resolved
after resolved
outer
resolved
```

由于resolve指定的是异步操作成功后的回调函数，它需要等所有同步代码执行后才会执行，因此最后打印'resolved'，这个和例2.2是一样的道理。

## 基本API

### .then()


> 语法：Promise.prototype.then(onFulfilled, onRejected)


对promise添加onFulfilled和onRejected回调，并返回的是一个新的Promise实例（不是原来那个Promise实例），且返回值将作为参数传入这个新Promise的resolve函数。

因此，我们可以使用链式写法，如上文的例2.7。由于前一个回调函数，返回的还是一个Promise对象（即有异步操作），这时后一个回调函数，就会等待该Promise对象的状态发生变化，才会被调用。

### .catch()

> 语法：Promise.prototype.catch(onRejected)

该方法是.then(undefined, onRejected)的别名，用于指定发生错误时的回调函数。

```
/* 例3.3 */
promise.then(function(data) {
    console.log('success');
}).catch(function(error) {
    console.log('error', error);
});

/*******等同于*******/
promise.then(function(data) {
    console.log('success');
}).then(undefined, function(error) {
    console.log('error', error);
});
```

```
/* 例3.4 */
var promise = new Promise(function (resolve, reject) {
    throw new Error('test');
});
/*******等同于*******/
var promise = new Promise(function (resolve, reject) {
    reject(new Error('test'));
});

//用catch捕获
promise.catch(function (error) {
    console.log(error);
});
-------output-------
Error: test
```

从上例可以看出，reject方法的作用，等同于抛错。

promise对象的错误，会一直向后传递，直到被捕获。即错误总会被下一个catch所捕获。then方法指定的回调函数，若抛出错误，也会被下一个catch捕获。catch中也能抛错，则需要后面的catch来捕获。

```
/* 例3.5 */
sendRequest('test.html').then(function(data1) {
    //do something
}).then(function (data2) {
    //do something
}).catch(function (error) {
    //处理前面三个Promise产生的错误
});
```

上文提到过，promise状态一旦改变就会凝固，不会再改变。因此promise一旦fulfilled了，再抛错，也不会变为rejected，就不会被catch了。

```
/* 例3.6 */
var promise = new Promise(function(resolve, reject) {
  resolve();
  throw 'error';
});

promise.catch(function(e) {
   console.log(e);      //This is never called
});
```

如果没有使用catch方法指定处理错误的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应（Chrome会抛错），这是Promise的另一个缺点。

```
/* 例3.7 */
var promise = new Promise(function (resolve, reject) {
    resolve(x);
});
promise.then(function (data) {
    console.log(data);
});
```
![chrome不报错](https://segmentfault.com/img/bVDEBJ?w=1466&h=286 "chrome不报错")
![safari报错](https://segmentfault.com/img/bVDEZp?w=530&h=151 "safari报错")
![safari报错](https://segmentfault.com/img/bVDE0o?w=388&h=145 "safari报错")
只有Chrome会抛错，且promise状态变为rejected，Firefox和Safari中错误不会被捕获，也不会传递到外层代码，最后没有任何输出，promise状态也变为rejected。

### .all()

> 语法：Promise.all(iterable)

该方法用于将多个Promise实例，包装成一个新的Promise实例。

> var p = Promise.all([p1, p2, p3]);

Promise.all方法接受一个数组（或具有Iterator接口）作参数，数组中的对象（p1、p2、p3）均为promise实例（如果不是一个promise，该项会被用Promise.resolve转换为一个promise)。它的状态由这三个promise实例决定。

- 当p1, p2, p3状态都变为fulfilled，p的状态才会变为fulfilled，并将三个promise返回的结果，按参数的顺序（而不是 resolved的顺序）存入数组，传给p的回调函数，如例3.8。
- 当p1, p2, p3其中之一状态变为rejected，p的状态也会变为rejected，并把第一个被reject的promise的返回值，传给p的回调函数，如例3.9。

```
/* 例3.8 */
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 3000, "first");
});
var p2 = new Promise(function (resolve, reject) {
    resolve('second');
});
var p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 1000, "third");
}); 

Promise.all([p1, p2, p3]).then(function(values) { 
  console.log(values); 
});

-------output-------
//约 3s 后
["first", "second", "third"] 
```

```
/* 例3.9 */
var p1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, "one"); 
}); 
var p2 = new Promise((resolve, reject) => { 
  setTimeout(reject, 2000, "two"); 
});
var p3 = new Promise((resolve, reject) => {
  reject("three");
});

Promise.all([p1, p2, p3]).then(function (value) {
    console.log('resolve', value);
}, function (error) {
    console.log('reject', error);    // => reject three
});

-------output-------
reject three
```

这多个 promise 是同时开始、并行执行的，而不是顺序执行。从下面例子可以看出。如果一个个执行，那至少需要 1+32+64+128

```
/* 例3.10 */
function timerPromisefy(delay) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve(delay);
        }, delay);
    });
}
var startDate = Date.now();

Promise.all([
    timerPromisefy(1),
    timerPromisefy(32),
    timerPromisefy(64),
    timerPromisefy(128)
]).then(function (values) {
    console.log(Date.now() - startDate + 'ms');
    console.log(values);
});
-------output-------
133ms       //不一定，但大于128ms
[1,32,64,128]
```

### .race()

> 语法：Promise.race(iterable)

该方法同样是将多个Promise实例，包装成一个新的Promise实例。

> var p = Promise.race([p1, p2, p3]);

Promise.race方法同样接受一个数组（或具有Iterator接口）作参数。当p1, p2, p3中有一个实例的状态发生改变（变为fulfilled或rejected），p的状态就跟着改变。并把第一个改变状态的promise的返回值，传给p的回调函数。

```
/* 例3.11 */
var p1 = new Promise(function(resolve, reject) { 
    setTimeout(reject, 500, "one"); 
});
var p2 = new Promise(function(resolve, reject) { 
    setTimeout(resolve, 100, "two"); 
});

Promise.race([p1, p2]).then(function(value) {
    console.log('resolve', value); 
}, function(error) {
    //not called
    console.log('reject', error); 
});
-------output-------
resolve two

var p3 = new Promise(function(resolve, reject) { 
    setTimeout(resolve, 500, "three");
});
var p4 = new Promise(function(resolve, reject) { 
    setTimeout(reject, 100, "four"); 
});

Promise.race([p3, p4]).then(function(value) {
    //not called
    console.log('resolve', value);              
}, function(error) {
    console.log('reject', error); 
});
-------output-------
reject four
```

在第一个promise对象变为resolve后，并不会取消其他promise对象的执行，如下例


```
/* 例3.12 */
var fastPromise = new Promise(function (resolve) {
    setTimeout(function () {
        console.log('fastPromise');
        resolve('resolve fastPromise');
    }, 100);
});
var slowPromise = new Promise(function (resolve) {
    setTimeout(function () {
        console.log('slowPromise');
        resolve('resolve slowPromise');
    }, 1000);
});
// 第一个promise变为resolve后程序停止
Promise.race([fastPromise, slowPromise]).then(function (value) {
    console.log(value);    // => resolve fastPromise
});
-------output-------
fastPromise
resolve fastPromise
slowPromise     //仍会执行
```

### .resolve()

语法：
```
Promise.resolve(value);
Promise.resolve(promise);
Promise.resolve(thenable);
```

它可以看做new Promise()的快捷方式。

```
Promise.resolve('Success');

/*******等同于*******/
new Promise(function (resolve) {
    resolve('Success');
});
```

这段代码会让这个Promise对象立即进入resolved状态，并将结果success传递给then指定的onFulfilled回调函数。由于Promise.resolve()也是返回Promise对象，因此可以用.then()处理其返回值。

```
/* 例3.13 */
Promise.resolve('success').then(function (value) {
    console.log(value);
});
-------output-------
Success
```

```
/* 例3.14 */
//Resolving an array
Promise.resolve([1,2,3]).then(function(value) {
  console.log(value[0]);    // => 1
});

//Resolving a Promise
var p1 = Promise.resolve('this is p1');
var p2 = Promise.resolve(p1);
p2.then(function (value) {
    console.log(value);     // => this is p1
});
```

Promise.resolve()的另一个作用就是将thenable对象（即带有then方法的对象）转换为promise对象。

```
/* 例3.15 */
var p1 = Promise.resolve({ 
    then: function (resolve, reject) { 
        resolve("this is an thenable object!");
    }
});
console.log(p1 instanceof Promise);     // => true

p1.then(function(value) {
    console.log(value);     // => this is an thenable object!
  }, function(e) {
    //not called
});
```

再看下面两个例子，无论是在什么时候抛异常，只要promise状态变成resolved或rejected，状态不会再改变，这和新建promise是一样的。

```
/* 例3.16 */
//在回调函数前抛异常
var p1 = { 
    then: function(resolve) {
      throw new Error("error");
      resolve("Resolved");
    }
};

var p2 = Promise.resolve(p1);
p2.then(function(value) {
    //not called
}, function(error) {
    console.log(error);       // => Error: error
});

//在回调函数后抛异常
var p3 = { 
    then: function(resolve) {
        resolve("Resolved");
        throw new Error("error");
    }
};

var p4 = Promise.resolve(p3);
p4.then(function(value) {
    console.log(value);     // => Resolved
}, function(error) {
    //not called
});
```

### .reject()

> 语法：Promise.reject(reason)

这个方法和上述的Promise.resolve()类似，它也是new Promise()的快捷方式。

```
Promise.reject(new Error('error'));

/*******等同于*******/
new Promise(function (resolve, reject) {
    reject(new Error('error'));
});
```

这段代码会让这个Promise对象立即进入rejected状态，并将错误对象传递给then指定的onRejected回调函数。


# Promise常见问题

经过上一章的学习，相信大家已经学会使用Promise。
总结一下创建promise的流程：

1. 使用new Promise(fn)或者它的快捷方式Promise.resolve()、Promise.reject()，返回一个promise对象
2. 在fn中指定异步的处理
处理结果正常，调用resolve
处理结果错误，调用reject

如果使用ES6的箭头函数，将会使写法更加简单清晰。

这一章节，将会用例子的形式，以说明promise使用过程中的注意点及容易犯的错误。

**情景1**：reject 和 catch 的区别
- promise.then(onFulfilled, onRejected)
在onFulfilled中发生异常的话，在onRejected中是捕获不到这个异常的。
- promise.then(onFulfilled).catch(onRejected)
.then中产生的异常能在.catch中捕获

一般情况，还是建议使用第二种，因为能捕获之前的所有异常。当然了，第二种的.catch()也可以使用.then()表示，它们本质上是没有区别的，.catch === .then(null, onRejected)

**情景2**：如果在then中抛错，而没有对错误进行处理（即catch），那么会一直保持reject状态，直到catch了错误

```
/* 例4.1 */
function taskA() {
    console.log(x);
    console.log("Task A");
}
function taskB() {
    console.log("Task B");
}
function onRejected(error) {
    console.log("Catch Error: A or B", error);
}
function finalTask() {
    console.log("Final Task");
}
var promise = Promise.resolve();
promise
    .then(taskA)
    .then(taskB)
    .catch(onRejected)
    .then(finalTask);
    
-------output-------
Catch Error: A or B,ReferenceError: x is not defined
Final Task
```

```
/* 例4.2 */
function taskA() {
    console.log(x);
    console.log("Task A");
}
function taskB() {
    console.log("Task B");
}
function onRejectedA(error) {
    console.log("Catch Error: A", error);
}
function onRejectedB(error) {
    console.log("Catch Error: B", error);
}
function finalTask() {
    console.log("Final Task");
}
var promise = Promise.resolve();
promise
    .then(taskA)
    .catch(onRejectedA)
    .then(taskB)
    .catch(onRejectedB)
    .then(finalTask);
    
-------output-------
Catch Error: A ReferenceError: x is not defined
Task B
Final Task
```

将例4.2与4.1对比，在taskA后多了对A的处理，因此，A抛错时，会按照A会按照 taskA → onRejectedA → taskB → finalTask这个流程来处理，此时taskB是正常执行的。

**情景3**：每次调用then都会返回一个新创建的promise对象，而then内部只是返回的数据
```
/* 例4.3 */
//方法1：对同一个promise对象同时调用 then 方法
var p1 = new Promise(function (resolve) {
    resolve(100);
});
p1.then(function (value) {
    return value * 2;
});
p1.then(function (value) {
    return value * 2;
});
p1.then(function (value) {
    console.log("finally: " + value);
});
-------output-------
finally: 100

//方法2：对 then 进行 promise chain 方式进行调用
var p2 = new Promise(function (resolve) {
    resolve(100);
});
p2.then(function (value) {
    return value * 2;
}).then(function (value) {
    return value * 2;
}).then(function (value) {
    console.log("finally: " + value);
});
-------output-------
finally: 400
```

第一种方法中，then的调用几乎是同时开始执行的，且传给每个then的value都是100，这种方法应当避免。方法二才是正确的链式调用。
因此容易出现下面的错误写法：

```
/* 例4.4 */
function badAsyncCall(data) {
    var promise = Promise.resolve(data);
    promise.then(function(value) {
        //do something
        return value + 1;
    });
    return promise;
}
badAsyncCall(10).then(function(value) {
   console.log(value);          //想要得到11，实际输出10
});
-------output-------
10
```

正确的写法应该是：

```
/* 改写例4.4 */
function goodAsyncCall(data) {
    var promise = Promise.resolve(data);
    return promise.then(function(value) {
        //do something
        return value + 1;
    });
}
goodAsyncCall(10).then(function(value) {
   console.log(value);
});
-------output-------
11
```

**情景4**：在异步回调中抛错，不会被catch到
```
// Errors thrown inside asynchronous functions will act like uncaught errors
var promise = new Promise(function(resolve, reject) {
  setTimeout(function() {
    throw 'Uncaught Exception!';
  }, 1000);
});

promise.catch(function(e) {
  console.log(e);       //This is never called
});
```

**情景5**： promise状态变为resove或reject，就凝固了，不会再改变
```
console.log(1);
new Promise(function (resolve, reject){
    reject();
    setTimeout(function (){
        resolve();            //not called
    }, 0);
}).then(function(){
    console.log(2);
}, function(){
    console.log(3);
});
console.log(4);

-------output-------
1
4
3
```

# 结语
通过阅读这篇文章，让我对promise有个大概的认识。文章出处：[https://segmentfault.com/a/1190000007032448#articleHeader16](https://segmentfault.com/a/1190000007032448#articleHeader16)