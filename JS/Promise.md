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

