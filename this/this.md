# this

大多数时候,this的值由函数的调用者来决定.
运行时不可能通过赋值操作修改this的值,而且不同时刻对同一个函数的调用,其中的this所指也可能不同.
可以通过调用函数的`call()`,`apply()`和`bind()`方法来修改this的指向.
this的值始终指向调用函数的对象.


### 全局上下文

当函数在全局作用域执行时,this指向window对象.
在严格模式下,全局作用域下的this为undefined.
看看如下例子:

```javascript
var myFunciton = function(){
  console.log(this);
  console.log(this=== window);
}

myFunction();// Output: Window object. In strict mode value will be undefined 
```

上述函数的执行不在任何函数或对象内,所以默认情况下myFunciton是在全局作用域下被执行.
所以输出显示this指向window,在严格模式下,是undefined.


![this in the global context](./CodeSnippets/thisGlobal.jpg)

In strict mode, value of this will be undefined
![Value of this in strict model](./CodeSnippets/UseStrict.jpg)

### 作为对象的方法时

当函数以某个对象方法的身份执行时,this指向这个对象.

为了理解这个说法,看看下个例子:

```javascript
var val = 37;
var myObj = {
	val : 10,
	someFunction : function(){
		console.log(this.val);//Output: 10 since tha value of this is equal to myObj
		console.log(window.val); //Output: 37
		console.log(this === myObj) // true
		console.log(this)//Output: myObj object
	}
}

myObj.someFunction();
```
#### Console output:
![](./CodeSnippets/this.jpg)

再看一个例子:

```javascript
//Variable defined in global scope
var val = 37;

var myObj = {
	val : 10
}

var someFunction = function(){
		console.log(this.val);
		console.log(window.val);
		console.log(this === myObj);
		console.log(this);
		console.log(this === window) 
}

myObj.objectFunc = someFunction;
myObj.objectFunc();  
/*
Output:
	1. 10
	2. 37
	3. true
	4. myObj
	5. false
*/

someFunction();
/*
Output:
	1. 37
	2. 37
	3. false
	4. window
	5. true
*/
```
上面这个例子,第一次`someFunction`是以`myObj`的方法的身份执行的,然后,又在全局上下文执行了一次.
结果显示同一个函数的`this`的值是由运行时的执行上下文动态决定的.

### 作为构造器时

当一个函数作为构造器时(使用new关键字调用时),`this`指向它将要构造的新对象.

```JavsScript

function ConstructorFunc(value){
	this.someValue = value;
}

var obj1 = new ConstructorFunction(20);
console.log(obj1.someValue)//Output: 20
```
![](./CodeSnippets/constructorThis.jpg)

这里,我们定义了`ConstructorFunc`,并创建了一个对象.执行new语句时,this将指向新创建的对象.

### 在立即执行函数中(IIFE)

在立即执行函数中,this始终指向window对象,看个例子:

```JavaScript
// IIFE outside any function 
(function() {
  console.log(this); // Output: Window object
})()


// IIFE inside an object function
var obj = {};

var someFunc = function() {
  console.log("Functions this");
  console.log(this === obj);
  console.log('++++++++++++++++++++++++++');
  
  // IIFE 
  (function() {
    console.log("IIFE this");
    console.log(this); // Output: Window object
    console.log("IIFE");
   })()
};

obj.func = someFunc;

obj.func();
```

#### Console Output
![](./CodeSnippets/IIFE_first.png)

在第一个例子中,IIFF不在任何函数中,其中的this指向window对象.

![](./CodeSnippets/IIFE_second.png)

这一次,IIFE函数的定义位于`someFunc`中,this依然指向window对象.

这是因为函数中this的值由调用它的对象决定的.`someFunc`被`obj`调用,所以其中的this指向`obj`.
而IIFE时自调用,它没有被任何对象调用.所以它的this指向window对象.

### 在事件回调中

在事件回调中,`this`的值等于触发事件的元素对象.看个例子:

```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<body>
	<div id="divId">
		Hello
	</div>
	<script type="text/javascript">
		var id = document.getElementById('divId').addEventListener('click', clickMe, false);
		function clickMe(event) {
			console.log(event.currentTarget);
			console.log("CLick Me");
			console.log(this);
		}
	</script>
</body>
</html>
```

上面这个例子中,`div`元素添加了click事件的监听,回调`clickMe`函数.

![](./CodeSnippets/EventHandler.png)

当点击`div`时,从上面的控制台输出可以看出,this指向了点击的`div`元素.

this的值在运行时可以通过`call`,`apply`和`bind`函数改变.这将在其他文章里讨论.
