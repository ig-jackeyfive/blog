## 问题

今天在写项目时发现了一个console.log的有时会出现异步问题的坑，具体代码是这样的

```javascript
getByTreeId({ fileType: "document", treeId: obj.treeId }).then(
    (res) => {
        console.log("document", res.data);
        if (res.data == null) {
            this.docbtn = "添加";
            // this.temp3 = this.temp;
        } else {
            this.docbtn = "修改";
            let temp = res.data;
            if (temp.tags) {
                temp.tags = temp.tags.map((item) => {
                    return item.labelName;
                });
            }
            if (temp.fileUrls) {
                temp.fileUrls = temp.fileUrls.map((item) => {
                    return {
                        name: item.fileName,
                        url: item.url,
                    };
                });
            }
            this.temp3 = temp;
        }
    }
);
```

就是一个简单的请求接口，可以看到我在最前面就将请求会的数据res就打印出来了。按理来说应该是会将res正确的打印出来，但是事实确是打印出来的数据不一致。

## 原理

这个不一致就是console.log异步打印造成的后果。

`console.log`打印的是对象当时的快照，展开对象时，它其实是重新去内存中读取对象的属性值。

举个例子：

```javascript
var a = {
	num: 1
}
console.log(a);
a.num++
```

将上面这串代码放在浏览器的控制台里，

浏览器执行到第二部代码时，打印出a对象的**快照**

此时浏览器显示：

> ▶{num:1}

哎，这没问题呀？

但是当我们展开这个对象时就会发现是这样的

>▼{num：1}**i**
>
>​		num:2
>
>​	▶____proto____: **Object**

展开的num值变成了2.

这是因为打开对象的时候，数据是从内存堆中去取值的，而此时代码已经跑完，num的值已经加一，所以显示的值是2。

## 分析

现在回过来分析我的代码。

原来是我在后面的代码中将res的数据进行了处理，更改。

这里我忘记了将res.data深复制给下面的temp，这就导致直接其实temp和res.data其实是指向的同一个地址，所以对temp的值操作修改后就导致res所引用的值也发生了更改。

又加上console.log的’异步‘打印。打印的只是res对象的快照，当我展开对象时展示的就是处于内存堆里已经修改过的值。

所以虽然我是在最前面就将res打印出来，但实际上确实打印的处理修改后的res。

```javascript
let temp = res.data;
if (temp.tags) {
    temp.tags = temp.tags.map((item) => {
        return item.labelName;
    });
}
if (temp.fileUrls) {
    temp.fileUrls = temp.fileUrls.map((item) => {
        return {
            name: item.fileName,
            url: item.url,
        };
    });
}
```

## 结论

console.log打印出来的内容不一定正确,因为打印的是对象的**快照**。但普通类型`number、string、boolean、null、undefined`的打印输出确实可靠的，对于`Object`等引用类型来说，则就会出现上述异步打印输出的情况。