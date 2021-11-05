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