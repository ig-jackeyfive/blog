# **new：**

https://segmentfault.com/a/1190000009286643?utm_source=sf-similar-article

拓展：

call，apply原理：

把调用的函数添加到要绑定的对象的属性上，改变this指向，调用函数然后`delete`属性，返回值



# bind()

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