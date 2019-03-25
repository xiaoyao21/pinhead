## 块级作用域的绑定

var声明变量提升机制
提升机制：通过var声明的变量，都会被当成在 ``当前作用域顶部声明`` 的变量。
```js
function student(name){
	console.log(sex);  //undefined
		if(name){
			var sex="men";
			return sex;
		}else{
			console.log(sex);  //undefined
			//return null;
		}
	console.log(sex);  //undefined
	}
student();
```
在此处不管if里面判断条件true还是false变量name都会被创建。

### 块级声明
块级声明的变量在指定块的作用域之外无法访问。
块级作用域存在于：

* 函数内部
* 块中(字符 { 和 } 之间的区域)

## let，const声明
两者的相同点：

**1. 不会被提升**

**2. 同一作用域中 重复声明报错**
```js
let a="1";
let a="2";
console.log(a);// 报错


let a="1";
console.log(a);  //1
{
	let a="2"
	console.log(a);  //2
}
```

**3. 存在代码块**
let，const声明的变量只在当前的代码块内有效，一但执行到代码块的外部就会被销毁。
   
**4. 不绑定全局作用域**
当var声明的变量被用于全局作用域时，它会创建一个新的全局变量作为全局对象的属性(window对象)。
但是对于let const，会在全局作用域下创建一个新的绑定，但该绑定不会添加为全局对象的属性。
```js
var value="hello!"
console.log(window.value);  //hello!


const value="hello!"
console.log(window.value);  //undefined
```

下来说一下 let 和 const 的区别：

**const 用于声明常量，其值一旦被设定不能再被修改，声明的常量必须进行初始化。**

``* 注意：const 声明不允许修改绑定（即：不可以为其定义的常量再赋值，就算是同类型数据也不可以），但允许修改值。这意味着当用 const 声明对象时，可以修改（包括添加属性值）对象的属性值。``

```js
const obj={
	name:"xiaoming",
	sex:"men"
}
// obj={
// 	name:"xiaohong"   //报错
// }
obj.name="xiaohong";  //属性值的修改
console.log(obj.name);  //xiaohong

obj.teacher="li";  //属性值的添加
console.log(obj.teacher);  //li
```

## 临时死区(TDZ)
let 和 const 声明的变量不会被提升到作用域顶部，如果在声明之前访问这些变量，会导致报错：

这是因为 JavaScript 引擎在扫描代码发现变量声明时，要么将它们提升到作用域顶部(遇到 var 声明)，要么将声明放在 TDZ 中(遇到 let 和 const 声明)。访问 TDZ 中的变量会触发运行时错误。只有执行过变量声明语句后，变量才会从 TDZ 中移出，然后方可访问。
```js

                          let/const -->放入TDZ  -->执行后 -->变量从TDZ中移出
JS引擎（发现变量声明）-->
                              var   -->声明提升
```
```js
console.log(typeof value); //undefined 

{
console.log(typeof value); //此时变量再 TDZ 报错
let value = 1;
}
```

## 循环中的块级作用域

``let``
```js
//这是一个我们常见的问题
var funcs = [];
for (var i = 0; i < 3; i++) {
    funcs[i] = function () {
        console.log(i);
    };
}
funcs[0](); // 3
```

```js
//用立即函数 解决上述闭包问题
var funcs = [];
for (var i = 0; i < 3; i++) {
    funcs[i] = (function(value){
        return function() {
            console.log(value);
        }
    }(i))
}
funcs[0](); // 0
```
在循环内部，立即执行函数为接受的每一个变量i都创建一个副本并保存为变量value 
```js
//用ES6中let解决上述问题  栗1
var funcs = [];
for (let i = 0; i < 3; i++) {
    funcs[i] = function () {
        console.log(i);
    };
}
funcs[0](); // 0
```
在这里let解决闭包的原因是什么呢？我们先暂且将问题先放一放~~
先来看另一个栗子：
```js
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
 *注意： let 声明在循环内部的行为是标准中专门定义的，不一定就与 let 的不提升特性有关
 
其实呢上面的代码是这样解释的：就是在 for (let i = 0; i < 3; i++) 中，即圆括号之内建立一个隐藏的作用域。
 ``每次迭代循环时都创建一个新变量，并以之前迭代中同名变量的值将其初始化``
 
 通俗的来说~~ 每次循环的时候let都会 **创建一个新的变量 i** 并将其初始化为i的当前值。

栗1 的代码就相当于：
 
```js
// 伪代码
(let i = 0) {
    funcs[0] = function() {
        console.log(i)
    };
}

(let i = 1) {
    funcs[1] = function() {
        console.log(i)
    };
}

(let i = 2) {
    funcs[2] = function() {
        console.log(i)
    };
};
```
``const``
那我们将上面的list改为const呢？

```js
var funcs = [];
for (const i = 0; i < 10; i++) {
    funcs[i] = function () {
        console.log(i);
    };
}
funcs[0](); //报错  因为const中的绑定不可被修改
    
```

``for in``
我们上面说的都是普通的for循环，那在for in循环中呢？
```js
var funcs = [], object = {a: 1, b: 1, c: 1};
for (var key in object) {
    funcs.push(function(){
        console.log(key)
    });
}

funcs[0]() 

```
用var声明打印结果为'c'

用let声明，结果为'a'

const呢,它没有报错，输出结果还是'a'这是因为在 for in 循环中，每次迭代不会修改已有的绑定，而是会 **创建一个新的绑定**。