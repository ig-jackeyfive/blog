# 问题

在写vue项目的时候，为了项目有良好的兼容性，在ie内核里的浏览器中测试是必不可少的，因为IE这个毒瘤很多东西都不支持。

今天我就在项目中发现发送请求更改数据后页面里的**数据不刷新**，我一度以为是IE不兼容es6语法的原因，不支持promise语法。花了大量的时间去让配置es6-promise、babel-polyfill等。

```javascript
//增加一行的数据然后再次去请求刷新数据结果数据还是没增加之前的数据
updateData() {
            this.$refs["dataForm"].validate((valid) => {
                if (valid) {
                    const tempData = Object.assign({}, this.temp);
                    updateMeasure(tempData).then(() => {
                        this.$alert("", {
                            title: "",
                            message: "修改成功",
                            type: "success",
                            duration: 2000,
                        });
                        getPage(this.listQuery).then((res) => {
                            console.log(res);
                            this.list = res.data.records;
                            this.total = res.data.total;
                        });
                    });
                }
            });
        },
```

# 原因

我打开控制台才发现原来更新数据后重新发送的获取数据的请求**没有发出去**。

![image-20210331224155659](C:%5CUsers%5CLENOVO%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210331224155659.png)

就是上面的增加数据成功后的再次获取数据的getPage请求没有发出去

```javascript
 getPage(this.listQuery).then((res) => {
                            console.log(res);
                            this.list = res.data.records;
                            this.total = res.data.total;
 });
```

之后百度一查，原来是因为

**IE浏览器在遇到相同的url的get请求时，不会将请求发出去，而是从缓存里面找已有的上次那个get请求。所以返回的数据还是上一次的请求的数据。**

# 解决办法

知道错误的原理之后就好办了，我们可以在get请求后面加一个**时间戳**就行了。

这样每个get请求都不会重复，浏览器就不会不把它发出去了。

```javascript
//url后面加一个${new Date().getTime()或者？{new Date().getTime()
export function getPage(data) {
    return request({
        //url: `/measure/page?keyword=${data.keyword}&page=${data.page}&limit=${data.limit}`,这是原来的url
        url: `/measure/page?keyword=${data.keyword}&page=${data.page}&limit=${data.limit}`&date=${new Date().getTime()，//这是加了时间戳后的url
        method: 'get', //query
    })
}
```

