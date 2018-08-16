#Inheritance in JavaScript
#JS中的继承

## Prototype Chaining
## 原型链

>JavaScript does not have classes unlinke other languages. It uses the concept of prototypes for inheritance.
>To understand how JavaScript implements inheritance, go through [this artice](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part2/Prototypes.md) to understand how prototypes work.
>Prototype chaining means an objects dunder proto or __proto__ will point to another object instead of 
>pointing to the constructor function prototype. If the other object's dunder proto or __proto__ property points to another object
>it will results into chain. This is prototype chaining. 

JS不像其他语言,没有`class`(ES6 和 TS 引入了 `class`关键字). 它使用原型的概念来实现继承.
原型链是指对象的dunder proto(`\_\_proto\_\_`)指向另一个对象,而不是指向构造器的原型,并且这种解构循环下去就是一条链.

![Prototype chaining](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part4/CodeSnippets/ProtoTypeChaining.jpg)

Let's implement prototype chaining
来个例子

```javascript
//SuperType constructor function
function SuperType(){
	this.name = "Virat"
}

//SuperType prototype
SuperType.prototype.getSuperName = function(){
	return this.name
}

//SubType prototype function
function SubType(){
	this.age = 26
}

//Inherit the properties from SuperType
SubType.prototype = new SuperType();

//Add new property to SubType prototype
SubType.prototype.getSubAge = function(){
	return this.age;
}

//Create a SubType object
var subTypeObj = new SubType();
console.log(subTypeObj.name); //Output: Virat
console.log(subTypeObj.age); //Output: 26
console.log(subTypeObj.getSuperName()); //Output: Virat
console.log(subTypeObj.getSubAge()); //Output: 26
```
>Above code defines two consructor functions, SuperType and SubType. 
>By default, *SubType.prototype* has a *constructor* function which points to the constructon function itself
>and *__proto__* property which inherits the default object properties

上述代码定义了两个构造器,`SuperType`和`SubType`.
默认情况下,`SubType.prototype`有一个属性为`constructor`,指向构造器函数本身,以及一个`dunder proto`属性指向所继承的对象.

```javascript
//Inherit the properties from SuperType
SubType.prototype = new SuperType();
```

>Above line rewrites the default prototype of the SubType constructon function 
>and makes SubType.prototype to point to an object of SuperType constructor function.
>This means that all the properties and methods that typically exists on an instance of SuperType now also on SubType.prototype
>This means that now, SubType function has access to all the SuperType properties and methods

这一行代码修改了`SubType`的默认原型对象,让其指向了一个由`SuperType`构造器生成的对象.
这意味着`SupertType`的实例拥有的属性和方法,现在`SubType`的原型都有了,即现在`SubType`可以访问任意的`SuperType`的属性和方法.

```javascript
//Add new property to SubType prototype
SubType.prototype.getSubAge = function(){
	return this.age;
}
```
>After the default prototype of SubType constructor function has been overwritten, by using the above line of code
>we add a new method *getSubAge()* on top of what was inherited from SuperType, to the prototype object of SubType constructor function

修改了`SubType`的prototype后,上述代码又给其添加了新的方法`getSubAge()`

>**Note**: New methods must be added to the SubType after the inheritance 
>because inheritance overwrites the existing prototype of SubType

**注意**给`SubType`的新方法必须在完成继承操作之后添加,因为继承操作会覆盖其原型.

![Inheritance using prototype chaingin](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part4/CodeSnippets/ProtoChainInheritance.jpg)

#### Console Output
>SuperType.prototype

![SuperType prototype](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part4/CodeSnippets/SuperTypePrototype.jpg)

>SubType prototype

![SubType.prototype](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part4/CodeSnippets/SubTypeProtoType.jpg) 

>SubType constructor

![SubType constructor](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part4/CodeSnippets/SubTypeConst.jpg)

>***Note***: getSuperName() method remains on the SuperType.prototype object, but name property ends up on
SubType.prototype. That’s because getSuperName() is a prototype method, and property is
an instance property. SubType.prototype is now an instance of SuperType, so property is stored
there. Also note that SubType.prototype.constructor points to SuperType, because the constructor
property on the SubType.prototype was overwritten.

**注意**: `getSuperName()`方法依旧来自`SuperType`的原型上,但属性`name`来自`SubType`的原型.这是因为`getSuperName()`是一个原型上的方法,
而属性`name`是实例上的属性.`SubType`的原型时`SuperType`的一个实例,所以属性`name`来自这里.
同时注意到,`SubType`原型上的constructor指向了`SuperType`,这是因为其constructor属性在继承时被覆盖了.

#### Problems with prototype chaining
#### 原型链存在的问题

>As all the properties of the super type prototype are shared among the child objects, if one child modifies the property of the 
>Super type prototype, other childs also gets affected. This issue has been explined in greate details 

`SuperType`原型上的所有属性被其子对象所共享,如果有一个子对象修改了其中的属性值,其他子对象也会被影响.

[here](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part2/Prototypes.md)

>To fix this issue, we use constructor to inherit the instance properties and prototype chaining to to inherit methods and share properties

为避免这个问题,可以使用构造器来继承实例属性,使用原型链来继承共享的方法和属性.

```javascript
//SuperType constructor function
function SuperType(firstName, lastName){
	this.firstName = firstName || "Virat",
	this.lastName = lastName || "Kohli",
	this.friends = ["Ashwin", "Jadeja"]
}

//SuperType prototype
SuperType.prototype.getSuperName = function(){
	return this.firstName + " " + this.lastName;
}

//SubType prototype function
function SubType(firstName, lastName, age){
	//Inherit instance properties
	SuperType.call(this, firstname, lastName);
	this.age = age;
}

//Inherit methods and shared properties
SubType.prototype = new SuperType();

//Add new property to SubType prototype
SubType.prototype.getSubAge = function(){
	return this.age;
}

//Create SubType objects
var subTypeObj1= new SubType("Virat", "Kohli", 26);
var subTypeObj2 = new SubType("Sachin", "Tendulkar", 39);

//Modify the friends property using the subTypeObj1
subTypeObj1.friends.push("Amit");

console.log(subTypeObj1.friends);//["Ahswin", "Jadega", "Amit"]
console.log(subTypeObj2.friends);//["Ashwin", "Jadega"]

//subTypeObj1
console.log(subTypeObj1.firstName); //Output: Virat
console.log(subTypeObj1.age); //Output: 26
console.log(subTypeObj1.getSuperName()); //Output: Virat Kohli
console.log(subTypeObj1.getSubAge()); //Output: 26

//subTypeObj2
console.log(subTypeObj2.firstName); //Output: Sachin
console.log(subTypeObj2.age); //Output: 39
console.log(subTypeObj2.getSuperName()); //Output: Sachin Tendulkar
console.log(subTypeObj2.getSubAge()); //Output: 39
```
>Let's try to understand the code
>First, we have defined a SuperType constructor function with firstName, lastName and friends as instance properties
>Then we defined a superName property on prototype of SuperType

来看看上面的代码,上面定义了一个`SuperType`构造器,其拥有`firstName`,`lastName`和`friends`三个实例属性.
然后在`SuperType`的原型上定义了一个`getSuperName`属性

>Now, let's look how we define the SubType constructr function

接下来是`SubType`构造器的定义

```javascript
//SubType prototype function
function SubType(firstName, lastName, age){
	//Inherit instance properties
	SuperType.call(this, firstname, lastName);
	this.age = age;
}
```
>Here, we define a SubType constructor function. 
>Inside the SubType constructor function, we call the SuperType constructor function with call.
>Call executes the SuperType constructor function in contecxt of the object begin created using the SubType constructor fucntion
>After inheriting the instance properties of the SuperType, we add one age property to the SubType constructor function

这里,在`SubType`构造器的定义中,使用了`call`方法显式地调用了`SupertType`构造器.
`call`方法会将`SuperType`的属性copy到`SubType`的实例上.
之后又在`SubType`构造器里添加了新属性`age`.

```javascript
//Inherit methods and shared properties
SubType.prototype = new SuperType();
```
>So far we have just inherited all the instance properties of the SuperType constructor function, but the shared properties and methods 
of the SuperType constructor function are still not inherited. We inherit them using the above lines of code. 

到这里,已经将`SuperType`构造器上的所有实例属性都已继承,但是共享的属性和方法还没有继承,上面这行代码就是来完成这件事的.

>Once the above lines of code are executed, we have inherited all the properties of the SuperType constructor function

执行完上面这行代码,我们就完成了对`SuperType`的所有属性的继承.

```javascript
//Add new property to SubType prototype
SubType.prototype.getSubAge = function(){
	return this.age;
}
```

>Using the above lines of code, we add a new property to SubType prototype on top of the methods and properties inherited 
>from the SuperType constructor function

这行代码给`SubType`又添加了一个新方法

>Let's understand the whole process by creating an object using SubType constructor function

接下来通过创建一个`SubType`的实例,来看看上面这整个过程都做了些什么

```javascript
var subTypeObj1= new SubType("Virat", "Kohli", 26);
```

>When we execute the above line of code, all the three parameters(Virat, Kohli and 26) are passed to the SubType constructor function.
>SubType constructor function, then calls SuperType constructor function using call *SuperType.call(this, firstname, lastName)*
>*this* here represent the *subTypeObj1*

执行上面这行代码,三个参数('Virat', 'Kohli', 26)传给了`SubType`构造器.`SubType`构造器通过执行`SuperType.call(this, firstName, lastName)`这行代码,调用了`SuperType`构造器
这里的`this`指代`subTypeObj1`

>SuperType constructor function is executed in the context of *subTypeObj1* and add propeties firstName, lastName, friends to the *subTypeObj1* object
>After return of *SuperType.call(this, firstname, lastName)*, SubType constructor function adds a *age* property to *subTypeObj1* object.

这里`SuperType`构造器的执行上下文是`subTypeObj1`,属性`firstName`,`lastName`,`friends`添加给了`subTypeObj1`对象.
这之后,`SubType`构造器又给`subTypeObject1`添加了新属性`age`.

>Thus as of now there are properties with the *subTypeObj1* object (firstName, lastName and age).
Currently SubType constructor function has following methods and shared propertes in its prototype property:
1. getSuperName()
2. getSubAge

至此,`subTypeObj1`对象拥有了属性`firstName`,`lastName`和`age`.
构造器`SubType`的原型对象有了如下方法和共享属性.

*subTypeObj1* inherits all these properties from SubType constructor function. 

看看`subTypeObj1`对象的结构:

```javascript
console.log(subTypeObj1)
```

>Console output

![](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part4/CodeSnippets/subTypeObj1.jpg)
