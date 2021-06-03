**JS中的+=与=+是两种截然不同的运算符号**

+= 很常见，就是简单的**自增**操作。比如b+=a就是b=a+b

``` javascript
var a = 1,b = 1;

b += a;

console.log(b); //1
```



而=+，比如a=+b,是将b转换为**数字**，赋值给a

``` javascript
//a是字符串
var a = '32',b;
b = +a;
console.log(b)//32

//a是单元素数组
a=[3]
b=+a;
console.log(b)//3

//a是布尔值
a=true
b=+a
console.log(b)//1
```

