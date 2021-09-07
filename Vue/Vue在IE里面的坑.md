在用vue写项目的时候，往往在IE内核的浏览器里面有很多烦人的坑😡😡😡😡😡，

烦死了。

# 1.FormData

在向后端传送数据的时候，经常会用到FormData，但是IE确只支持构造FormData接口，但是FormData的一些方法确不支持，如get（）、set（）、keys（）等

![image-20210319184544617](C:%5CUsers%5CLENOVO%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210319184544617.png)

# 2.Promise

IE不支持promise等es语法。在Vue官网里有提到

> 2.1.0 起新增：如果没有提供回调且在支持 Promise 的环境中，则返回一个 Promise。请注意 Vue 不自带 Promise 的 polyfill，所以如果你的目标浏览器不是原生支持 Promise (IE：你们都看我干嘛)，你得自行 polyfill。



那么要使我们的项目在Ie支持promise等es6语法，我们可以

1.安装 babel-polyfill 

> npm install --save babel-polyfill

2.在main.js引入 babel-polyfill

> import "babel-polyfill";

# 3.Url

IE的向后端发送的请求url有中文时会乱码。

这时要将Url通过encodeURI（）进行编码成为**统一资源标识符 (URI)**

# 4. select

在IE里, select的option是不支持onclick事件滴

# 5.el-table

/* 解决el-table在ie中不显示 */

.el-table {

-ms-flex: none !important;

flex: none !important;

height: max-content;

}

/* 解决el-table在ie宽度不能100% */

.el-table__header{

  width: 100% !important;

}

.el-table__body{

  width: 100% !important;

}