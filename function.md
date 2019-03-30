﻿
## es6函数

## 函数形参的默认值
js中在函数定义中声明了多少形参，都可以传入任意数量的参数。（形参实参的个数可以不统一）
```js
function fan(x,y){
	console.log(x,y);  //1 undefined
}
fan(1); //未赋值的函数参数默认值为undefined
```
## ES6中默认参数值
即没有参数传入值为其提供一个初始值：

* 可以为任意参数指定默认值，默认参数值的位置无要求，可以在指定默认值的参数后继续声明无默认值的参数
* 参数变量是默认声明的，所以不能用let或const再次声明。
```js
function foo(x = 5) {  
//在该函数作用域内不能重复声明 ?????? 参数x = 5形成一个单独作用域。实际执行的是let x = 5
  let x = 1; // error
  const x = 2; // error
}
foo();
```
上面代码中，参数变量x是默认声明的，在函数体中，不能用let或const再次声明，否则会报错。 

* 使用参数默认值时，函数不能有同名参数。 
```js
// 不报错
function foo(x, x, y) {
  // ...
}

// 报错
function foo(x, x, y = 1) {  
  // ...
}
// SyntaxError: Duplicate parameter name not allowed in this context
```

* null是一个合法的默认参数值 ( ``如果传入undefined，将触发该参数等于默认值``，null则没有这个效果。)
```js
function foo(x = 5, y = 6) {
  console.log(x, y);
}

foo(undefined, null)
// 5 null
```
* ``函数的length属性``，将返回 ``没有指定默认值的参数个数``。（也就是说，指定了默认值后，length属性将失真。）
```js
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function(a, ...b) {}).length  // 1
(function (a, b, c = 5) {}).length // 2
```
如果设置了默认值的参数不是尾参数，那么length属性也不再计入后面的参数了。
```js
(function (a = 0, b, c) {}).length // 0
(function (a, b = 1, c) {}).length // 1
```
## 默认参数下参数的作用域
一旦设置了参数的默认值，``函数进行声明初始化时(调用函数的时候)?????，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。`` 这种语法行为，在不设置参数默认值时，是不会出现的。

* 默认参数的临时死区
```js
function add(first=second,second){
	console.log(first+second) 
}
add(1,1);  //2
add(undefined,1);  //报错
```
```js
调用add(1,1) 时js代码
let first=1;
let second=1

调用add(undefined,1) 时js代码
let first=second;
let second=1
```
first 初始化时second还未初始化（处于临时死区），此时引用临时死区的绑定会报错。
* node : 函数参数有自己的作用域和临时死区，其与函数体的作用域是相互独立的，所以参数默认值不可以访问函数体内声明的变量。

## 默认参数值对arguments的影响
ES5非严格模式下，命名参数的变化会同步更新到arguments对象 （记录函数实参的数据）
ES5严格模式下，无论参数如何改变，arguments对象不再随之改变
ES6 一个函数使用默认参数，arguments对象的行为将与ES5严格模式下保持一致。即argumens对象与命名参数分离

```js
function fan(a,b,c,d){
	a=3;
	console.log(arguments) //3 2 3 4  arguments对象的值将虽随参数的变化而更新
}
fan(1,2,3,4);

function fan(a,b=0,c,d){
	a=3;
	console.log(arguments) //1 2 3 4  //不会更新 仍为实参的初始值
}
fan(1,2,3,4);
```
### 不定参数
* 每个函数最多只能声明一个不定参数
* 声明的不定参数要放在所有的参数末尾

*注 ：无论是否使用不定参数，arguments对象总是包含所有传入函数的参数。 
## name 属性
函数表达式的名字比函数本身被赋予的变量权重高
```js
var f = function () {};
f.name // "f"

const bar = function baz() {};
bar.name // "baz"
```
Function构造函数返回的函数实例，name属性的值为anonymous。
```
(new Function).name   //"anonymous"
```
bind返回的函数，name属性值会加上bound前缀。
```
function foo() {};
foo.bind({}).name // "bound foo"

(function(){}).bind({}).name // "bound "
```
## 块级函数
ES6严格模式下 ： ``在定义函数的代码块内，块级函数会被提升至顶部。``一旦执行完毕，函数定义会被立刻清除。
```
"use strict";
if(true){
    console.log(typeof fan)  //function
    function fan(){
    
    }
}
console.log(typeof fan) //undefined

```
ES6非严格模式下：不再将函数提升至代码块的顶端，而是提升自外围函数或全局作用域的顶端。
 上面的栗子将打印出两个 function。
 
##  ES6 箭头函数

箭头函数 ``()=>{}``

* 如果只有一个参数（）可以省 (注意：此处必须有且只有一个参数)

* 如果只有一个return {} 连带return可以省

* 如果需要直接返回一个对象
```
let func = (value, num) => ({total: value * num});
```

 **1. 没有this**
 
箭头函数本身没有this，所以需要通过查找作用域链来确定 this 的值。（其this值取决于该函数外部非箭头函数的this值)

如果 ``箭头函数被非箭头函数包含，this绑定的是最近一层非箭头函数的this`` ，否则其this值会被设置未undefined。

因为箭头函数没有 this，所以也不能用 call()、apply()、bind() 这些方法改变 this 的指向。
```js
var value = 1;
console.log(window.value); //1

var result = (() => this.value).bind({value: 2})();
console.log(result); // 1
```

 **2. 没有arguments**
 
箭头函数没有自己的 arguments 对象，所以 ``其始终可以访问外围函数的arguments对象``。
```js
function fan(){
	return ()=>{console.log(arguments[0])}
}
var fan1=fan(5)
fan1(2,3);  //5
```
 **3. 不能通过 new 关键字调用**
 
JavaScript 函数有两个内部方法：[[Call]] 和 [[Construct]]。

* 当通过 new 调用函数时，执行 [[Construct]] 方法，创建一个实例对象，然后再执行函数体，将 this 绑定到实例上。

* 当直接调用的时候，执行 [[Call]] 方法，直接执行函数体。
```
function name(sex){
	this.sex=sex
	}
	var teacher=new name("男");
	var student=name("女");
	
	console.log(teacher,student);//name {sex: "男"} undefined
	
	
function name(sex){
	this.sex=sex
	return sex
	}
	var teacher=new name("男");
	var student=name("女");
	
	console.log(teacher,student);//name {sex: "男"} "女"
```

箭头函数并没有 [[Construct]] 方法，不能被用作构造函数，如果通过 new 的方式调用，会报错。

注* 没有 new.target值，没有原型（undefined），没有super。

总的来说：箭头函数表达式的语法比函数表达式更短，并且不绑定自己的this，arguments，super或 new.target。

## 自执行函数
```
//一般形式
(function(){
    console.log(1)
})()

(function(){
    console.log(1)
}())
```
正常的立即执行函数表达式即可以用小括号包裹函数体，也可以额外包裹函数调用部分。
```
(() => {
    console.log(1)
})()
```
利用箭头函数简化立即执行函数只能按上述方法写。