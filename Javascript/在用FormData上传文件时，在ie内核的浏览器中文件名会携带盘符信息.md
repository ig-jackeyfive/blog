# 在用FormData上传文件时，在ie内核的浏览器中文件名会携带盘符信息

如下图，这是一个post请求的request请求头的信息，用的是formdata的参数格式。可以看到**filename**文件名携带了盘符信息，就是文件的绝对地址。这就很蛋疼。

![image-20210602223031967](C:%5CUsers%5CLENOVO%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210602223031967.png)

## 解决方法

通过查阅**MDN**官网的[formdata.append()](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/append)方法，可以看到append方法还可以传入第三个参数：文件名（filename）

![image-20210602223642226](C:%5CUsers%5CLENOVO%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210602223642226.png)

![image-20210602223753708](C:%5CUsers%5CLENOVO%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210602223753708.png)

本来filename的默认值是文件的名称，但是IE这个毒瘤非得加上一大串的盘符信息。

所以在上传文件时要兼容IE还得将filename的值自己传一遍。这样就解决ie中上传文件名携带盘符信息（即是文件在本机的绝对路径）的问题了。

```javascript
fd.append("files",file,file.name)//第二个参数为file对象，第三个参数为文件名
```

效果：

![image-20210602224429169](C:%5CUsers%5CLENOVO%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210602224430932.png)