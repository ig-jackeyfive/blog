# 参数传递

1. 按值传递

2. 按引用传递（传递的是对象的引用的**副本**）

   ``` javascript
   var obj = {
       value: 1
   };
   function foo(o) {
       o = 2;
       console.log(o); //2
   }
   foo(obj);
   console.log(obj.value) // 1
   ```

   

# call和apply的模拟实现

**call()**

> call () 方法在使用一个指定的 **this** 值和若干个指定的参数值的前提下调用某个函数或方法

``` javascript
Function.prototype.call2 = function(context) {
    var context = context || window; //context为null时指向window
    //context为要改变的this指向
    context.fn = this //this为调用call2的函数示例
    var args = [];
    for (var i = 1, len = arguments.length; i < len; i++) {
        args.push(arguments[i]);
    }
    // 可能有返回值
    let res = context.fn(...args);
    // eval('context.fn(' + args + ')');
    delete context.fn
    return res
}
```

**apply()**

apply() 方法与 call() 方法非常相似.

不同之处是：

call() 方法分别接受参数。

apply() 方法接受数组形式的参数。

``` javascript
Function.prototype.apply2 = function(context, arr) {
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    } else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push(arr[i]);
        }
        result = context.fn(...args)
    }

    delete context.fn
    return result;
}

```

# null不是对象

解释: 虽然 typeof null 会输出 object，但是这只是 JS 存在的一个悠久 Bug

# 0.1+0.2为什么不等于0.3？

0.1和0.2在转换成二进制后会无限循环，由于标准位数的限制后面多余的位数会被截掉，此时就已经出现了精度的损失，相加后因浮点数小数位的限制而截断的二进制数字在转换为十进制就会变成0.30000000000000004。

## JS数据类型

## 概念篇

### 1.JS原始数据类型有哪些？引用数据类型有哪些？

symbols 是一种无法被重建的基本类型。这时 symbols 有点类似与对象创建的实例互相不相等的情况，但同时 symbols 又是一种无法被改变的基本类型数据。

`Symbol`类型是为了解决属性名冲突的问题，顺带还具备模拟私有属性的功能。

### 2.说出下面运行的结果，解释原因。

``` javascript
function test(person) {
  person.age = 26
  person = {
    name: 'hzj',
    age: 18
  }
  return person
}
const p1 = {
  name: 'fyq',
  age: 19
}
const p2 = test(p1)
console.log(p1) // -> ?
console.log(p2) // -> ?

```

### 3.null是对象吗？为什么？

不是

解释: 虽然 typeof null 会输出 object，但是这只是 JS 存在的一个悠久 Bug

### 4.'1'.toString()为什么可以调用？

### 5.0.1+0.2为什么不等于0.3？

### 6.如何理解BigInt?

什么是BigInt?

为什么需要BigInt?

如何创建并使用BigInt？

值得警惕的点

## 检测篇

### 1. typeof 是否能正确判断类型？

### 2. instanceof能否判断基本数据类型？

### 3. 能不能手动实现一下instanceof的功能？

核心: 原型链的向上查找。

### 4. Object.is和===的区别？

Object在严格等于的基础上修复了一些特殊情况下的失误，具体来说就是+0和-0，NaN和NaN。 源码如下：

```javascript
function is(x, y) {
  if (x === y) {
    //运行到1/x === 1/y的时候x和y都为0，但是1/+0 = +Infinity， 1/-0 = -Infinity, 是不一样的
    return x !== 0 || y !== 0 || 1 / x === 1 / y;
  } else {
    //NaN===NaN是false,这是不对的，我们在这里做一个拦截，x !== x，那么一定是 NaN, y 同理
    //两个都是NaN的时候返回true
    return x !== x && y !== y;
  }
```



## 转换篇

### 1. [] == ![]结果是什么？为什么？

### 2. JS中类型转换有哪几种？

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/20/16de9512eaf1158a~tplv-t2oaga2asx-watermark.awebp)

### 3. == 和 ===有什么区别？

### 4. 对象转原始类型是根据什么流程运行的？

对象转原始类型，会调用内置的[ToPrimitive]函数，对于该函数而言，其逻辑如下：

1. 如果Symbol.toPrimitive()方法，优先调用再返回
2. 调用valueOf()，如果转换为原始类型，则返回
3. 调用toString()，如果转换为原始类型，则返回
4. 如果都没有返回原始类型，会报错

### 5. 如何让if(a == 1 && a == 2)条件成立？

## 闭包

### 什么是闭包

闭包是指有权访问另外一个函数作用域中的变量的函数

### 闭包产生的原因?

作用域链...

闭包产生的本质就是，当前环境中存在指向父级作用域的引用。

### 闭包有哪些表现形式?

1. 返回一个函数
2. 函数作为参数传递
3. 回调函数
4. IIFE

### 如何解决下面的循环输出问题？

```javascript
for(var i = 1; i <= 5; i ++){
  setTimeout(function timer(){
    console.log(i)
  }, 0)
}
```

1. IIFE
2. 给定时器传入第三个参数，作为timer函数的参数
3. let块级作用域

## 原型链

### 1.原型对象和构造函数有何关系？

在js中，当定义一个函数数据类型（函数，类）的时候，会自带一个prototype属性，这个属性指向函数的原型对象

当使用new关键字对函数进行调用的时候，函数就成为了构造函数，会返回一个全新的实例对象，这个实例对象有一个`__proto__`属性，该属性指向函数的原型对象

new原理：生成一个新的空对象，让该对象的原型对象`__proto__`指向函数的原型对象Constructor.prototype，然后通过Constructor.call()改变this指向到新的对象，使改对象能访问构造函数的属性。

**new：**

https://segmentfault.com/a/1190000009286643?utm_source=sf-similar-article

拓展：

call，apply原理：

把调用的函数添加到要绑定的对象的属性上，改变this指向，调用函数然后`delete`属性，返回值



bind()

>`bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

特性：

- 可以指定`this`
- 返回一个函数
- 可以传入参数
- 柯里化

```javascript
Function.prototype.bind1 = function(context) {
    if (typeof this !== 'function') {
        throw new Error('what is trying to be bound is not callable')
    }
    //context为要绑定的this
    let _this = this //调用函数
    let args = Array.prototype.slice.call(arguments, 1) //参数列表

    let func = function() {}

    let fBound = function() {
        args = args.concat(Array.prototype.slice.call(arguments))
        return _this.apply(this instanceof fBound ? this : context, args)
    }
    func.prototype = _this.prototype
    fBound.prototype = new func()
    return fBound
}
```

注意：

* 指定this返回一个函数
* 传参
* 柯里化
* 绑定函数可new
* function判断



bind和call，apply的不同：

`bind` 方法与 `call / apply` 最大的不同就是前者返回一个绑定上下文的**函数**，而后两者是**直接执行**了函数。

https://github.com/yygmind/blog/issues/23

### 2.能不能描述一下原型链？

JavaScript对象通过`__proto__`属性指向原型对象，直至指到Object对象为止（Object指向null），这样就形成了原型指向的链条，这就是原型链。

* hasOwnProperty() 	对象自身
* in    对象中（包含原型链）



## 继承

### 1.Call

无法继承方法

### 2.原型链

实例使用了同一个原型对象。

### 3.Call+原型链

```javascript
 function Parent3 () {
    this.name = 'parent3';
    this.play = [1, 2, 3];
  }
  function Child3() {
    Parent3.call(this);//1
    this.type = 'child3';
  }
  Child3.prototype = new Parent3();//2
  var s3 = new Child3();
  var s4 = new Child3();
  s3.play.push(4);
  console.log(s3.play, s4.play);
```

父类构造函数会多执行了一次

###  4.组合继承的优化

```javascript
 function Parent4 () {
    this.name = 'parent4';
    this.play = [1, 2, 3];
  }
  function Child4() {
    Parent4.call(this);
    this.type = 'child4';
  }
  Child4.prototype = Parent4.prototype;
//子类实例的构造函数会显示为Parent4
//显然这是不对的，应该是Child4
```

### 5.(最推荐使用): 组合继承的优化

``` javascript
  function Parent5 () {
    this.name = 'parent5';
    this.play = [1, 2, 3];
  }
  function Child5() {
    Parent5.call(this);
    this.type = 'child5';
  }
  Child5.prototype = Object.create(Parent5.prototype);
  Child5.prototype.constructor = Child5;
```

这是最推荐的一种方式，接近完美的继承，它的名字也叫做寄生组合继承。

## 类数组

常见的类数组

* arguments
* 用getElementsByTagName/ClassName()获得的**HTMLCollection**
* 用querySelector获得的**nodeList**



转换成数组

### 1. Array.prototype.slice.call()

### 2. Array.from()

### 3. ES6展开运算符

### 4. 利用concat+apply



## 如何中断forEach循环？

1.使用**try**监视代码块，在需要中断的地方抛出异常。

2.官方推荐方法（替换方法）：用every和some替代forEach函数。every在碰到return false的时候，中止循环。some在碰到return true的时候，中止循环



## JS判断数组中是否包含某个值

### array.indexOf

### array.includes(searcElement[,fromIndex])

### array.find(callback[,thisArg])

### array.findeIndex(callback[,thisArg])



## JS中flat---数组扁平化

### 1. 调用ES6中的flat方法

### 2. replace + split

### 3. replace + JSON.parse

### 4. 普通递归

### 5. 利用reduce函数迭代

### 6：扩展运算符



## 高阶函数

#### 1.map



#### 2. reduce

#### 3. filter

#### 4. sort



## 谈谈你对JS中this的理解

> **this是在执行上下文创建时确定的一个在执行过程中不可更改的变量**
>
> **执行上下文**，就是JavaScript引擎在执行一段代码之前将代码内部会用到的一些**变量**、**函数**、**this**提前声明然后保存在变量对象中的过程。

优先级: new > call、apply、bind > 对象.方法 > 直接调用。



**显示绑定**：call，apply，bind

**隐式绑定**：

* 全局上下文
* 直接调用函数
* 对象.方法
* DOM事件绑定
* new+构造函数

箭头函数没有this



## JS中浅拷贝

### 1. 手动实现

```javascript
const clone = (target) => {
    if (typeof target == 'object' && target != null) {
        let res = Array.isArray(target) ? [] : {};
        for (let prop in target) {
            // console.log('prop', prop);
            // 只拷贝自身属性，不拷贝继承属性
            if (target.hasOwnProperty(prop)) {
                res[prop] = target[prop]
            }
        }
        return target
    } else {
        return target
    }
}
```

### 2. Object.assign

`**Object.assign()**` 方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回目标对象。

### 3. concat浅拷贝数组

### 4. slice浅拷贝

### 5. ...展开运算符



## 完整的深拷贝



JSON.parse(JSON.stringify())

坑：

* 无法拷贝一写`特殊的对象`，诸如 RegExp, Date, Set, Map等。
* 无法解决`循环引用`的问题
* 无法拷贝`函数`(划重点)

### 1. 简易版

```javascript
const deepClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? []: {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
          cloneTarget[prop] = deepClone(target[prop]);
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

### 2. 解决循环引用

### 3. 拷贝特殊对象

### 4. 拷贝函数



## 内存机制——数据是如何存储的

基本数据类型用`栈`存储，引用数据类型用`堆`存储。

> 闭包变量是存在堆内存中的。



## V8 引擎垃圾回收

不同语言垃圾回收：

* 自动回收：Java，JavaScript，python  

  采用自己的一套垃圾回收算法进行自动的内存管理

* 手动回收：C/C++    程序员自己去开辟或者释放内存



Javascript垃圾回收：

### **栈内存**

ESP指针下移，上下文切换之后，栈顶的空间会自动被回收



### **堆内存**

V8内存分配有上限源于	**JS单线程的执行机制	**和 	**JS垃圾回收机制的限制**



堆内存又分两部分进行处理：新生代内存和老生代内存

新生代就是临时分配的内存，存活时间短， 老生代是常驻内存，存活的时间长。

#### **新生代内存**

新生代内存采用**Scavenge算法**来处理：

1. 是把新生代空间对半划分为两个区域，一半是对象区域 ，一半是空闲区域。
2. 新加入的对象都会存放到对象区域，当对象区域快被写满时，就需要执行一次垃圾清理操作。
3. 垃圾回收过程中，首先要对对象区域中的垃圾做标记；标记完成之后，就进入垃圾清理阶段，副垃圾回收器会把这些存活的对象复制到空闲区域中，同时它还会把这些对象有序地排列起来，所以这个复制过程，也就相当于完成了内存整理操作，复制后空闲区域就没有内存碎片了。
4. 完成复制后，对象区域与空闲区域进行角色翻转，对象区域和空闲互换，好处是**两块区域无限重复使用**。
5. 为了执行效率，一般新生区的空间会被设置得比较小。
6. **对象晋升策略**：经过两次垃圾回收依然还存活的对象，会被移动到老生区中。

#### 老生代内存

当

- 已经经历过一次 Scavenge 回收。
- To（闲置）空间的内存占用超过25%。

时新生代中的变量就会被放入到`老生代内存`中，这种现象就叫`晋升`—— 对象晋升策略



第一步，**进行标记-清除**。

分成两个阶段，即标记阶段和清除阶段。

标记阶段：

清除阶段：



第二步，**整理内存碎片**。

把存活的对象全部往一端靠拢。



### 增量标记

为了降低老生代的垃圾回收而造成的卡顿，V8将一口气完成的标记任务分为很多小的部分完成，同时让垃圾回收标记和JavaScript应用逻辑交替进行，直到标记阶段完成。



##  V8 执行一段JS代码的过程

1. 首先通过词法分析和语法分析生成 `AST`
2. 将 AST 转换为字节码
3. 由**解释器**逐行执行字节码，遇到热点代码启动**编译器**进行编译，生成对应的机器码, 以优化执行效率





## 如何理解EventLoop

### 宏任务和微任务篇

**宏任务(MacroTask)**

普通任务队列和延迟队列中的任务，都属于**宏任务**。

* 普通队列

  * 渲染事件
  * 用户交互事件
  * js脚本执行
  * 网络请求、文件读写完成事件等等。

* 延迟队列

  * setTimeout/setInterval这样的定时器回调任务

  

**微任务(MicroTask)**

在每一个宏任务中定义一个**微任务队列**，当该宏任务执行完成，会检查其中的微任务队列，如果为空则直接执行下一个宏任务，如果不为空，则`依次执行微任务`，执行完成才去执行下一个宏任务。

* MutationObserver（提供了监视对DOM树所做更改的能力）
* Promise.then(或.reject) 
* 以 Promise 为基础开发的其他技术(比如fetch API)
* V8 的垃圾回收过程



### 浏览器篇

浏览器环境下 EventLoop 的执行流程：

1. **整个脚本**作为第一个宏任务执行
2. 执行过程中**同步代码**直接顺序执行，**宏任务**进入宏任务队列，**微任务**进入微任务队列
3. 当前宏任务执行完成，检查微任务队列，有则依次执行，直到微任务队列为空。
4. （一次宏任务代码和他的微任务代码执行完后）                                                            浏览器执行**UI线程**的渲染工作
5. 检查是否有**Web worker**任务，有则执行
6. 执行宏任务队列**队首新的**宏任务，回到2，依次循环，直到宏任务队列和微任务队列都为空。

https://www.bilibili.com/video/BV1kf4y1U7Ln?from=search&seid=5610041477041234462&spm_id_from=333.337.0.0

### nodejs篇

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/11/23/16e96b8587ad911d~tplv-t2oaga2asx-watermark.awebp)

各个阶段执行的任务如下：

- **timers阶段**：这个阶段执行setTimeout和setInterval预定的callback
- **I/O callback阶段**：执行除了close事件的callbacks、被timers设定的callbacks、setImmediate()设定的callbacks这些之外的callbacks
- **idle,prepare阶段**：仅node内部使用
- **poll阶段**：获取新的I/O事件，适当的条件下node将阻塞在这里
- **check阶段**：执行setImmediate()设定的callbacks
- **close callbacks阶段**：执行socket.on('close', ....)这些callbacks



nodejs中的定时器：

node版本 >= 11和浏览器一样，一个定时器运行完立即运行相应的微任务

node 版本小于 11 

>若第一个定时器任务出队并执行完，发现队首的任务仍然是一个定时器，那么就将微任务暂时保存，`直接去执行`新的定时器任务，当新的定时器任务执行完后，`再一一执行`中途产生的微任务。



**nodejs 和 浏览器关于eventLoop的主要区别**

浏览器中的微任务是在`每个相应的宏任务`中执行的，而nodejs中的微任务是在`不同阶段之间`执行的。





## nodejs中的异步、非阻塞I/O是如何实现的

`阻塞`和`非阻塞` I/O 其实是针对操作系统内核而言的。阻塞 I/O 的特点就是一定要**等到操作系统完成所有操作后才表示调用结束**，而非阻塞 I/O 是调用后立马返回，不用等操作系统内核完成操作。

nodejs中的异步 I/O 采用多线程的方式，由 `EventLoop`、`I/O 观察者`，`请求对象`、`线程池`四大要素相互配合，共同实现。



## JS异步编程有哪些方案

### 回调函数

### Promise

### co + Generator

### async + await



## 简单实现一下 node 中回调函数的机制



## Promise

### Promise 凭借什么消灭了回调地狱？

回调地狱

1. 多层嵌套的问题。
2. 每种任务的处理结果存在两种可能性（成功或失败），那么需要在每种任务执行结束后分别处理这两种可能性。

Promise解决手段

- **回调函数延迟绑定**。

  回调函数不是直接声明的，而是在通过后面的 then 方法传入的，即延迟传入。

- **返回值穿透**。

  内部返回promise对象穿透到外层

- **错误冒泡**。

  错误会一直向后传递，只用一个catch捕获错误就行



Promise第一版

```javascript
//定义三种状态
const PENDING = "pending";
const FULFILLED = "fulfilled";
const REJECTED = "rejected";

function MyPromise(executor) {
    let self = this; // 缓存当前promise实例
    self.value = null;
    self.error = null;
    self.status = PENDING;
    self.onFulfilled = null; //成功的回调函数
    self.onRejected = null; //失败的回调函数

    const resolve = (value) => {
        if (self.status !== PENDING) return;
        setTimeout(() => {
            console.log('开始处理resolve');
            self.status = FULFILLED;
            self.value = value;
            // 7.在这里调用第一个宏任务中已经挂载好的回调函数，这样就达到了延迟回调的效果
            self.onFulfilled(self.value); //resolve时执行成功回调
            console.log('resolve结束');
        });
    };

    const reject = (error) => {
        if (self.status !== PENDING) return;
        setTimeout(() => {
            self.status = REJECTED;
            self.error = error;
            self.onRejected(self.error); //reject时执行失败回调
        });
    };
    // 实例初始化，属性初始化完成后来到这里
    // 执行传入的executer函数，这个函数是一外部传入的要执行的代码，包含异步操作，
    // promise构造函数把resolve和reject处理函数传进去
    executor(resolve, reject);
    //  将异步函数成功和失败时要进行的操作传过去,
    //  成功时执行resolve函数，将promise实列的状态变成fulfilled
    //  失败时执行reject函数，将promise实例的状态变为rejected
}
MyPromise.prototype.then = function(onFulfilled, onRejected) {
    // 4.这时为pending状态，挂载onFulfilled，和onRejected即异步操作成功和失败时的回调函数到实例上
    if (this.status === PENDING) {
        this.onFulfilled = onFulfilled;
        this.onRejected = onRejected;
    } else if (this.status === FULFILLED) {
        //如果状态是fulfilled，直接执行成功回调，并将成功值传入
        onFulfilled(this.value)
    } else {
        //如果状态是rejected，直接执行失败回调，并将失败原因传入
        onRejected(this.error)
    }
    console.log('status', this.status); // 'pending'
    console.log('.then方法执行完成');
    return this;
}

const { log } = require('console');
const fs = require('fs')

// 1.首先创建promise实例
// 参数是executor函数，它接受两个函数参数
let promise1 = new MyPromise((resolve, reject) => {
    console.log('executer函数执行开始');
    let time1 = Date.now()
    console.log('1', time1);
    2. // 执行到这里，碰到异步操作，作为宏任务入队，
    fs.readFile('./1.txt', (err, data) => {
        console.log('异步操作的回调开始');
        let time2 = Date.now()
            //文件读取成功，进行成功时的回调函数resolve
        if (!err) {
            console.log('2', time2);
            console.log('time', time2 - time1);
            //6. 如果成功，程序来到resolve方法中
            resolve(data);
            console.log('resolve完');
        } else {
            //文件读取成功，进行失败时的回调函数reject
            console.log('2', time2);
            console.log('time', time2 - time1);
            reject(err);
        }
        console.log('3', Date.now());
    })
    console.log('executer函数执行完成');
});

// 3.主函数，即第一个宏任务继续执行，此时异步操作还未执行，在宏任务队尾躺着呢
// 继续执行实例方法，传入如果异步操作成功或失败时要处理的函数
// 程序来到来到MyPromise.prototype.then()方法
console.log('开始执行then方法');
let x1 = promise1.then(
    //8.执行self.onFulfilled
    data => {
        console.log("第一次展示", data.toString());
    }, err => {
        console.log(err);
    });
// 5.then方法执行完成，实例上面已经挂在好了onFulfilled，和onRejected即异步操作成功和失败时的回调函数
// 执行完第一个宏任务，接着执行后面的一个宏任务，即fs.readFile的异步操作
// 程序来到fs.readFile函数中
console.log(x1);


// executer函数执行开始
// 1 1636064218601
// executer函数执行完成
// 开始执行then方法
// status pending
// .then方法执行完成
// MyPromise {
//   value: null,
//   error: null,
//   status: 'pending',
//   onFulfilled: [Function (anonymous)],
//   onRejected: [Function (anonymous)]
// }
// 异步操作的回调开始
// 2 1636064218609
// time 8
// resolve完
// 3 1636064218611
// 开始处理resolve
// 第一次展示 1.txt的内容
// resolve结束
```



### Promise.all

```javascript
Promise.all1 = (promises) => {
    return new Promise((resolve, reject) => {
        let res = []
        let len = promises.length
        if (!len) resolve(res)
        for (let p of promises) {
            Promise.resolve(p).then(data => {
                res.push(data)
                if (p == promises[len - 1]) {
                    resolve(res)
                }
            }).catch(err => {
                reject(err)
            })
        }
    })

}
```



### Promise.race

```javascript
Promise.race1 = (promises) => {
    return new Promise((resolve, reject) => {
        if (!promises.length) return
        for (let p of promises) {
            Promise.resolve(p).then(data => {
                resolve(data)
                return
            }).catch(err => {
                reject(err)
                return
            })
        }
    })
}
```



## 生成器以及协程的理解

生成器是一个带`星号`的"函数"(注意：它并不是真正的函数)，它返回一个  [`Generator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Generator) 对象，可以通过`yield`关键字`暂停执行`和`恢复执行`的。

> yield 命令用于将程序的执行权移出 Generator 函数

```javascript
function* gen() {
  console.log("enter");
  let a = yield 1;
  let b = yield (function () {return 2})();
  let c = yield b
  return 3;
}
var g = gen() // 阻塞住，不会执行任何语句
console.log(typeof g)  // object  看到了吗？不是"function"
console.log(g.next())  
console.log(g.next())  
console.log(g.next(3))// 传参给上一个yield的左值 即b  
console.log(g.next())  
console.log(g.next()) 
// enter
// { value: 1, done: false }
// { value: 2, done: false }
// { value: 3, done: false }
// { value: 4, done: true }
// { value: undefined, done: true }
```

### yield*

将执行权移交给另一个生成器函数（当前生成器暂停执行，例 A 协程中将` JS 线程的控制权转交给 B协程`， B 执行，A 暂停）

### 生成器实现机制——协程

协程是一种比线程更为轻量级的存在，协程处在线程的环境中，一个线程可以有多个协程。协程可以理解为线程的一个个任务。不像进程和线程，协程不受操作系统的管理，而是由具体的应用程序代码所控制。



## 如何让 Generator 的异步代码按顺序执行完毕

### thunk 函数

thunk 即偏函数(**传名调用**的一种实现策略，**用来替换某个表达式**)

JS中的Thunk函数替换的不是表达式，而是**多参数函数**，将其替换成单参数的版本，且只接受回调函数作为参数。



用于Generator 函数的自动流程管理，自动执行 Generator 函数

thunk包含着**柯里化**概念

```javascript
const gen = function* () {
  const data1 = yield readFileThunk('001.txt')
  console.log(data1.toString())
  const data2 = yield readFileThunk('002.txt')
  console.log(data2.toString)
}
const readFileThunk = (filename) => {
  return (callback) => {
    fs.readFile(filename, callback);
  }
}
function run(gen){
  const next = (err, data) => {
    let res = gen.next(data);
    if(res.done) return;
    res.value(next);
  }
  next();
}
run(g);

```



### Promise

```javascript
function run(g) {
  const next = (data) => {
    let res = g.next();
    if(res.done) return;
    res.value.then(data => {
      next(data);
    })
  }
  next();
}
```

### 采用 co 库

**co**库核心原理就是上面的Promise情况下的执行代码

co的使用：

```javascript
const co = require('co');
let g = gen();
co(g).then(res =>{
  console.log(res);
})
```



## 解释一下async/await的运行机制

> MDN 的定义: async 是一个通过异步执行并隐式返回 Promise 作为结果的函数。

`async/await`被称为 JS 中**异步终极解决方案**。是es7的新语法。

**async 函数就是 Generator 函数的语法糖**

它将 Generator 函数的星号（*）替换成 async，将 yield 替换成 await。



原理：**将 Generator 函数和自动执行器，包装在一个函数里。**

用法：async关键字，表明该函数内部有异步操作。调用该函数时，会立即返回一个**Promise对象**，而不是直接返回值。await 关键字使JavaScript运行时暂停于此行（将JS线程的控制权转交给父协程），允许其他代码在此期间执行，直到异步函数调用返回其结果。

```javascript
//指定多少毫秒后输出一个值
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value)
}

asyncPrint('hello world', 50);
```

优点：

* 能够像 **co + Generator** 一样用同步的方式来书写异步代码
* **语义化**更加明显。比于之前的`Promise`不断调用then的方式，和语义化更加明显。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。
* **内置执行器**。无需第三方库。 Generator 函数的执行必须靠执行器，所以才有了 co 函数库，而 async 函数自带执行器
* **更广的适用性**。 co 函数库中yield 命令后面只能是 Thunk 函数或 Promise 对象。而async 函数的 await 命令后面 Promise 对象和原始类型的值都可以

缺点：

* `await` 关键字会阻塞其后的代码，直到promise完成，当代码有大量`await`的promises相继发生就会变慢。每个`await`都会等待前一个完成，而你实际想要的可能是所有的这些promises同时开始处理。

  * 解决：将 `Promise` 对象存储在变量中来同时开始它们

  * ```javascript
    //	阻塞的情况：耗时9秒
    function timeoutPromise(interval) {
      return new Promise((resolve, reject) => {
        setTimeout(function(){
          resolve("done");
        }, interval);
      });
    };
    async function timeTest() {
      await timeoutPromise(3000);
      await timeoutPromise(3000);
      await timeoutPromise(3000);
    }
    let startTime = Date.now();
    timeTest().then(() => {
      let finishTime = Date.now();
      let timeTaken = finishTime - startTime;
      console.log("Time taken in milliseconds: " + timeTaken);
      //Time taken in milliseconds: 9001
    })
    ```

  * ```javascript
    // `Promise` 对象存储在变量的形式。非阻塞：耗时3秒
    //	阻塞的情况：耗时9秒
    async function timeTest() {
      const timeoutPromise1 = timeoutPromise(3000);
      const timeoutPromise2 = timeoutPromise(3000);
      const timeoutPromise3 = timeoutPromise(3000);
    
      await timeoutPromise1;
      await timeoutPromise2;
      await timeoutPromise3;
    }
    let startTime = Date.now();
    timeTest().then(() => {
      let finishTime = Date.now();
      let timeTaken = finishTime - startTime;
      console.log("Time taken in milliseconds: " + timeTaken);
      //Time taken in milliseconds: 3002
    })
    ```

* 必须将等待执行的promise封装在异步函数中。



注意：

* 最好把 await 命令放在 **try...catch** 代码块中，因为await命令后的Promise对象运行结果可能是rejected

* await 命令只能用在 async 函数之中，否则报错

* forEach 方法的参数改成 async 函数，会并发执行，而不是继发执行。正确写法应用for循环。（原理：迭代器）

  如果确实希望多个请求并发执行，可以使用 Promise.all 方法。

参考：

https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous/Async_await

https://www.ruanyifeng.com/blog/2015/05/async.html



## forEach 中用 await 会产生什么问题?怎么解决这个问题？

**对于异步代码，forEach 并不能保证按顺序执行。**

```javascript
async function test() {
	let arr = [4, 2, 1]
	arr.forEach(async item => {
		const res = await handle(item)
		console.log(res)
	})
	console.log('结束')
}

function handle(x) {
	return new Promise((resolve, reject) => {
		setTimeout(() => {
			resolve(x)
		}, 1000 * x)
	})
}

test()
//结束
//1
//2
//4
```

forEach 方法的参数改成 async 函数，会并发执行，而不是继发执行。正确写法应用for循环。

```javascript
async function test() {
  let arr = [4, 2, 1]
  for(const item of arr) {
	const res = await handle(item)
	console.log(res)
  }
	console.log('结束')
}
//4
//2
//1
//结束
```

**原理：Iterator 迭代器**

```javascript
async function test() {
  let arr = [4, 2, 1]
  let iterator = arr[Symbol.iterator]();
  let res = iterator.next();
  while(!res.done) {
    let value = res.value;
    console.log(value);
    await handle(value);
    res = iterater.next();
  }
	console.log('结束')
}
// 4
// 2
// 1
// 结束
```

上面的for...of循环代码就是这段代码的语法糖



另外如果希望多个请求并发执行，可以使用 Promise.all 方法。

https://juejin.cn/post/6844904004007247880#heading-56
