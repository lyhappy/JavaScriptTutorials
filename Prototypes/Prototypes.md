# JavaScript中的原型

### 构造器函数存在的问题

在上一篇讨论了各种生成对象的方法。
其中一种是使用构造器函数,如下：

```javascript
function Human(firstName, lastName){
	this.firstName = firstName,
	this.lastName = lastName,
	this.fullName = function(){
		return this.firstName + " " + this.lastName;
	}
}
```
>Let's create objects ***person1*** and ***person2*** using the ***Human*** constructor function.

```javascript
var person1 = new Human("Virat", "Kohli");
var person2 = new Human("Sachin", "Tendulkar");
```

执行上述代码,JS引擎为实例`person1`和`person2`创建了属性`firstName`,`lastName`和方法`fullName`

![alt-text](./CodeSnippets/person1.png)
![alt-text](./CodeSnippets/person2.png)

如上,使用构造器生成的每个对象将拥有各自的属性和方法副本.这里存在两个完全做相同事情的`fullName`函数的实例是不合理的.
每个对象独立的存储函数实例是在浪费内存.接下来将看到如何解决这个问题.

### 原型

在JS中创建函数时,JS引擎会向生成的函数对象中添加一个特殊的属性`prototype`. 这个特殊属性的值是一个对象(被称作原型对象),它有一个默认属性`constructor`.
属性`constructor`的值指回了函数对象本身.
可以通过`functionName.prototype`来访问函数的原型属性.

![](./CodeSnippets/ConstructorPrototype.png)

如上图所示,构造器函数`Human`有一个`prototype`属性指向一个原型对象.
这个原型对象有一个`constructor`属性指回构造器函数`Human`.

```javascript
function Human(firstName, lastName){
	this.firstName = firstName,
	this.lastName = lastName,
	this.fullName = function(){
		return this.firstName + " " + this.lastName;
	}
}

console.log(Human);
```
#### Console output
![](./CodeSnippets/ObjectUsingConstructorFn.png)

可按如下方式访问构造器`Human`的prototype属性

```javascript
console.log(Human.prototype)
```
#### Console output
![](./CodeSnippets/HumanPrototype.png)

如上图所示,`Human`函数的`prototype`属性值是一个对象,有两个属性:

1. *constructor* property which points to  *Human* function itself
2. *\_\_proto\_\_* property - We will discuss about this while explaining *inheritance* in JavaScript

### 使用构造器函数创建对象

当JS引擎生成一个对象时,会向其添加一个属性为`\_\_proto\_\_`,这个属性称作`dunder proto`.这个属性指向构造器函数的原型对象


![](./CodeSnippets/HumanObjectProto.png)

如上图所示,构造器`Human`创建的对象`person1`有一个属性为`\_\_proto\_\_`,指向构造器函数的原型对象.

试试:

```javascript
//Create an object person1 using the Human constructor function
var person1 = new Human("Virat", "Kohli");
console.log(person1);
```
#### Console output

![alt text](./CodeSnippets/HumanProtoConsole.png)

使用`===`操作符可以检验实例对象的`\_\_proto\_\_`属性和构造器的`prototype`属性是完全相等的.

```javascript
Human.prototype === person1.__proto__ //true
```

再试一次

```javascript
var person2 = new Human("Sachin", "Tendulkar");
console.log(person2);
```
#### Console output
![](./CodeSnippets/person2HumanProto.png)

```javascript
Human.prototype === person2.__proto__ //true
```

```javascript
person1.__proto__ === person2.__proto__ //true
```

以上证明`person1`和`person2`的dunder proto与`Human`的prototype所指相同

![](./CodeSnippets/person12ConsProto.png)

**构造器函数与其生成的所有实例共享原型对象.**

### 原型对象

顾名思义,我们可以往构造器的原型对象上绑定属性和方法,这样,所有使用过该构造器生成的对象共享这些属性和方法

可以用点操作符或方括号操作符向构造器的原型对象上添加新属性:

```javascript
//Dot notation
Human.prototype.name = "Ashwin";
console.log(Human.prototype.name)//Output: Ashwin
//Square bracket notation
Human.prototype["age"] = 26;
console.log(Human.prototype["age"]); //Output: 26
console.log(Human.prototype);
```
#### Console output

![](./CodeSnippets/HumanProtAddProp.png)
新属性`name`和`age`已添加到`Human.prototype`上

#### Example

```javascript
//Create an empty constructor function
function Person(){

}
//Add property name, age to the prototype property of the Person constructor function
Person.prototype.name = "Ashwin" ;
Person.prototype.age = 26;
Person.prototype.sayName = function(){
	console.log(this.name);
}
//Create an object using the Person constructor function
var person1 = new Person();
//Access the name property using the person object
console.log(person1.name)// Output" Ashwin
```
当调用`console.log(person1.name)`时发生了什么?我们来检查下对象`person1`上是否有`name`属性.

```javascript
console.log(person1);
```
#### Console output
![](./CodeSnippets/personEmptyObject.png)

如上所示,`person1`是空,除了有一个`dunder proto`属性外,没有其他属性了.那么`console.log(person.name)`这行代码是怎么输出'Ashwin'的?

当试图访问对象的属性时,JS引擎首先会尝试在对象上查找属性;如果找到了,则返回其值.但是,如果属性没有出现在对象中,则会继续尝试在其原型对象或dunder proto上查找.
如果找到了,则返回其值,否则JS引擎会继续在dunder proto的dunder proto上继续查找,以此类推,直到这条链上的最后一个dunder proto为null,这时则返回`undefined`.

所以,当访问`person1.name`时,JS引擎会首先在`person`对象上检查是否存在属性`name`.这里,`person`对象上并没有`name`这个属性.
然后,JS引擎继续在`person`对象的dunder proto上继续查找,这一次,找到了,然后返回其值'Ashwin'.

再来创建一个对象`person2`

```javascript
var person2 = new Person();
//Access the name property using the person2 object
console.log(person2.name)// Output: Ashwin
```

现在,在对象`person1`上定义一个属性`name`并赋初始值.

```javascript
person1.name = "Anil"
console.log(person1.name)//Output: Anil
console.log(person2.name)//Output: Ashwin
```
出现这种情况时因为JS引擎已经在person1上找到了新定义的属性`name`,就不会在去其原型上查找了.也可以理解为person1的`name`属性遮盖了原型上的`name`属性.

person2上对`name`属性的查找依旧.

#### 原型存在的问题

构造器的原型对象被其创建的所有对象所共享,即其上的所有属性也被共享.
数据类型为基础类型的属性没什么问题,如上面例子,修改对象上的属性值时,一般会覆盖原型对象上的属性值.
但如果属性的数据类型为引用类型(Object, Array)时,就会出现意外.

如下:修改基础类型的属性没啥问题

```javascript
person1.name = "Ganguly"
console.log(perosn1.name);//Output: Ganguly
console.log(person2.name);//Output: Ashwin
```

#### 当原型对象包含引用类型的属性时,就会引发异常问题

```javascript
//Create an empty constructor function
function Person(){
}
//Add property name, age to the prototype property of the Person constructor function
Person.prototype.name = "Ashwin" ;
Person.prototype.age = 26;
Person.prototype.friends = ['Jadeja', 'Vijay'],//Arrays are of reference type in JavaScript
Person.prototype.sayName = function(){
	console.log(this.name);
}

//Create objects using the Person constructor function
var person1= new Person();
var person2 = new Person();

//Add a new element to the friends array
person1.friends.push("Amit");

console.log(person1.friends);// Output: "Jadeja, Vijay, Amit"
console.log(person2.friends);// Output: "Jadeja, Vijay, Amit"
```

这里,构造器`Person`的原型上有一个`friends`属性,其值是引用,指向一个字符串数组.
构造器生成的两个对象`person1`和`person2`共享了这个引用.然后`person1`通过这个引用属性`friends`向那个字符串数组中添加了一个字符串.
因为这个字符串数组只存在于`Person`的原型对象上,(其他访问都是通过引用间接访问的),这样上述修改引起的副作用是使用`person2`再访问`friends`属性时,
所得到也是被修改后的数组.(所有这些引用都指向同一个数组实例).
如果程序的本意是在对象间共享数组,那么这么做没啥问题.
但这里显示指不同的人有不同的朋友,所以不同的`Person`实例如果没有不同的`friends`副本,必然会引起数据的混乱.

#### 组合构造器和原型

为了解决原型和构造器各自的问题,可以组合它们

1. Problem with constructor: Every object has its own instance of the function
2. Problem with the prototype: Modifying a property using one object reflects the other object also

将共享的属性和方法定义在原型上,特有的属性和方法定义在构造器里.

```javascript
//Define the object specific properties inside the constructor
function Human(name, age){
	this.name = name,
	this.age = age,
	this.friends = ["Jadeja", "Vijay"]
}
//Define the shared properties and methods using the prototype
Human.prototype.sayName = function(){
	console.log(this.name);
}
//Create two objects using the Human constructor function
var person1 = new Human("Virat", "Kohli");
var person2 = new Human("Sachin", "Tendulkar");

//Lets check if person1 and person2 have points to the same instance of the sayName function
console.log(person1.sayName === person2.sayName) // true

//Let's modify friends property and check
person1.friends.push("Amit");

console.log(person1.friends)// Output: "Jadeja, Vijay, Amit"
console.log(person2.frinds)//Output: "Jadeja, Vijay"
```
![](./CodeSnippets/ConstProto.png)
