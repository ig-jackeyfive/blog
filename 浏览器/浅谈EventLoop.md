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



参考：

https://juejin.cn/post/6844904004007247880#heading-10