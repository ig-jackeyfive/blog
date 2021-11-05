# es6相关语法总结

##  Let命令

### 1.let声明的变量只在let命令所在的代码块(块级作用域）内有效

```javascript
{
    let a=1;
    var b=2;
}
a//ReferenceError:a is not defined
b//2
```

### 2.不存在变量提升

**var** 命令会发生**变量提升**现象,即变量可以在声明前使用，值为undefined

```javascript
console.log(a);//输出undefined
var a=2;

//相当于
var a
console.log(a);
a=2
```

而**let**命名不会发生**变量提升**，其声明的变量必须在声明后使用，否则就会形成**暂存性死区**。

```javascript
console.log(a);//ReferenceError
let a=2;
```

### 3.暂存性死区

> 只要在块级作用域内存在let命令，它所声明的变量就“绑定“（binding）这个区域，不再受外部的影响

```javascript
var a=123;
if(true){
    a='abc';//ReferenceError
    let a;
}
```

 **ES6明确规定，如果区块中存在let和const命令，则这个区块对这些命令声明的变量从一开始就形成封闭作用域。只要在声明之前使用这些变量，就会报错。**



值得注意的是，”暂存性死区“意味着 typeof 不再是一个百分之百安全的操作：

```javascript
typeof x;//输出undefined
```

```javascript
typeof x;//ReferenceError
let x;
//因为let命令声明的x变量之前都属于x的死区，只要使用都该变量就会报错。
```



总结：暂时性死区的本质就是，只要进入当前作用域，所要使用的变量就已存在，但是不可获取，只是等到声明变量的那一行代码出现，才可以获取和使用该变量。

### 4.不允许重复声明

let不允许在相同作用域内重复声明同一个变量。

```javascript
function(){
    let a=1;
    var a=2;
}//报错

function(){
    let a=1;
    let a=2;
}//报错

function(a){
    let a;
}//报错

function(a){
    {
        let a;
    }
}//不报错
```

## Const命令

const声明一个只读的常量，一旦声明，常量的值就不能改变

### const基本用法和let一样

* 声明的变量只在所在的块级作用域内有效。
* 不会变量提升
* 存在暂时性死区
* 不可重复声明

### const的本质

**const实际上保证的不是变量的值不得改动，而是变量指向的那个内存地址不得改动**

* 对于简单数据类型，值就保存在变量指向的内存地址中，等同于常量

* 而对于复合数据类型（主要是对象、数组），变量指向的内存地址保存的是一个指针，const保证的是这个指针不变。指针指向的数据结构是可变的。

  ```javascript
  cosnt a={};
  a.name='liu';//可以成功
  
  a={};//TypeError:"a" is read-only
  ```

  

## 模板字符串

模板字符串就是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来***定义多行字符串***，或者在***字符串中嵌入变量***

### 1.定义多行字符串

```javascript
let a='普通字符串'
//如果用普通字符串来定义多行会报错
//let b='普通字符串
//		普通字符串'

let c=`多行字符串
		多行字符串
		多行字符串`
console.log(a);
//普通字符串
console.log(c);
//多行字符串
//		多行字符串
//		多行字符串
//空格和缩进都会保留在输出中
```

如果要在模板字符串中使用反引号，则需在`前面添加反斜杠转义。

``` javascript
let a=`模板字符串中用反引号：\`
模板字符串`
```

### 2.在字符串中嵌入变量

将变量名写在**${}**中

```javascript
//传统写法：
let name='lxh'
let a='My name is '+name+'。'

//使用模板字符串
let b=`My name is ${name}。`
console.log(a);//My name is lxh。

console.log(b);//My name is lxh。
```



**${}**内不仅可以放入变量，还可以调用函数，只要是JavaScript表达式，都可以。

```javascript
//调用函数
function fn() {
    return 'lxh'
}
let b = `My name is ${fn()}。`
console.log(b);//My name is lxh。
```

注意：如果${}里面的值不是字符串，将会按照一般的规则转换成字符串。比如：大括号中是一个对象，则默认调用对象的toString方法

```javascript
let a={
    name:'lxh',
}
let b = `My name is ${a}。`
console.log(b);//My name is [object Object]。
```



## 扩展运算符

### 含义

扩展运算符是三个点（...)，它同rest参数的逆运算它将一个数组转为用逗号分隔的参数序列

```javascript
console.log(...[1,2,3])//1 2 3
console.log(1,...[2,3],4)//扩展运算符可以与普通的函数参数结合使用
//1 2 3 4
```

该参数主要用于函数调用：

```javascript
function add(x,y){
    return x+y
}
let numbers=[1,2]
let result=add(...numbers)//将数组转化为了参数序列，直接调用
console.log(result)//3
```

扩展运算符后面还可以放置表达式：

```javascript
const arr=[
    ...(1>0?[1,2]:[]),3
]
console.log(arr)//[1,2,3]
```

### 替代数组的apply方法

### 应用

#### 1.合并数组

```javascript
//es5合并数组
var arr1=[1,2]
var arr2=[3]
console.log(arr1.concat(arr2))//[1,2,3]

////es6合并数组
console.log([...arr1, ...arr2]);//[1,2,3]
```

#### 2.与结构赋值结合

#### 3.函数的返回值

JavaScript的函数只能返回一个值，如果需要返回多个值，只能返回数组和对象。而扩展运算符提供了一种变通方法。

```javascript
var dataFields =readDateField(database);

var d = new Date(...dataFields);
```

> 上面的代码从数据库取出一行数据，通过扩展运算符，直接将其传入构造函数Date

#### 4.字符串

扩展运算符可以将字符串转为真正的数组

```javascript
var arr1 = [...'hello']
console.log(arr1);//[ 'h', 'e', 'l', 'l', 'o' ]
```

这样写法能够正确的识别32位的Unicode字符