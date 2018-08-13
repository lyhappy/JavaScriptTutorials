# Objects in JavaScript
# JavaScript中的对象
### What are Objects in JavaScript
### 什么是JS中的对象

>An object in JavaScript is a collection of key-value pairs. Each key-value pair is called as a property. 
>A property can be a function, an array, an object itself or any primitive data type i.e. integer, string, etc.
>Functions in object are called as methods. 

JS中的对象是若干K-V对的集合,一个K-V对被称作对象的一个属性.
属性可以是一个函数, 一个数组, 对象自身或者基础数据类型(如: intger, string等).
对象中的函数称作方法.

>Consider a simple object:

例子:

```javascript
var human = {
	firstName: "Virat",
	lastName: "Kohli",
	age: 30,
	fullName: function(){
		return this.firstName + " " + this.lastName		
	}
}
```
>Here firstName, lastName, and fullName are properties of the same object i.e. **human**.
>Every human will have these properties but their values may be different i.e. firstName, lastName may 
>have different value for different human.

这里`firstName`, `lastName`和`fullName`是同一个对象`human`的属性.每个人都会有这些属性,但属性的值未必一样.

```javascript
console.log(human);
```
#### Console output
#### 控制台输出
![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/BasicObjectExample.png)

#### Properties of the object can be accessed using the following two notations:
#### 对象的属性可以使用如下两种操作符访问
1. Dot notation
2. Square bracket notation

1. 点操作符(.)
2. 方括号操作符([])

### Dot notation
### 点操作符

```javascript
human.firstName; //Output: Virat

human.fullName(); //Output: Virat Kohli
```
> New properties can be added using the dot notation as shown below:

添加新属性可按如下方式使用点操作符
```javascript
human.age = 27
human.getAge = function(){
	return this.age;
}
```

>Now console human and we will find new properties age and getAge() added to the human object as shown below:

现在可以在控制台中看到新的属性`age` 和 `getAget()` 已经添加到对象`human`上

```javascript
Console.log(human);
```
#### Console output 

![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/AddPropertiesUsingDotNotation.png)

### Square bracket notation
### 方括号运算符

```javascript
human["firstName"]; //Output: Virat

human["fullName"](); //Output: Virat Kohli
```
> New properties can be added using the Square bracket notation as shown below: 

按如下方式使用方括号运算符也可添加新属性到对象上:

```javascript
human["weight"] = 65
human.getWeight = function(){
	return this.weight;
}
```

![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/AddPropertiesSqBrackNotation.png)

> Properties can also be accessed using a variable having value equal to the property name as shown below: 

属性也可以通过[]运算符使用值为属性名的字符串变量来访问.

```javascript
var firstNameProperty = "firstName";

console.log(human[firstNameProperty]) // Output: Virat
```

> **Note**: Above method of using variable to access property names cannot be used to access properties of the object using dot notation

>**注意**: 上述使用变量访问对象属性的方式不适用于点运算符

```javascript
Console.log(human.firstNameProperty) //Output: undefined
```

>**Note**: If we try to access the methods without using the **()**, output will be method definition as shown below: 

>**注意**: 访问方法时,如果忘记使用 **()** (函数调用运算符), 输出的将是方法定义(如下图所示):

```javascript
console.log(human.fullName); //Output: function definition
```
#### Console output

![](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/accessMethodWithoutBrakcets.png)

>An object property name can be any valid JavaScript string, or anything that can be converted to a string, including the empty string. However, any property name that is not a valid Javascript identifier (for example, a property name that has a space or a hyphen, or that starts with a number) can only be accessed and added to the object property using the square bracket notation

对象属性名可以是任意合法的JS字符串,或任何可以转换为字符串的变量,包括空字符串.
但是,属性名如果不是一个合法的JS标识符(如, 包含空格,连字符或以数字开始的字符串),则只能通过方括号运算符来访问和添加属性．

```javascript
human["date of birth"] = "Nov 28";
human[12] = 12;
human.12 = 12; //gives error
console.log(human);
```

#### Console output

![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/propertyNameOtherThanString.png)

```javascript
console.log(human.12); //Gives error
console.log(human[12]); //Output: 12
```

## Different ways of creating a JavaScript object:
## 创建JS对象的不同方法:

### Using object literal
### 使用对象字面量

> **human** object created above is an example of creating an object using object literal

上面创建对象`human`的方式就是使用对象字面量

```javascript
var human = {
	firstName: "Virat",
	lastName: "Kohli",
	age: 30,
	fullName: function(){
		return this.firstName + " " + this.lastName		
	}
}
```
### Using new Object()
### 使用new运算符

```javascript
var human = new Object()
console.log(human);// Creates an empty object
```

> We can added as many properties as we want usign either the dot notation or the square bracket notation

使用new运算符生成一个空对象，然后使用点运算符或方括号运算符来添加属性．

```javascript
human.firstName = "Virat";
human.lastName = "Kohli";
human.age = 30;
human.fullName = function(){
	return this.firstName + " " + this.lastName;
}
console.log(human)
```

#### Console output

![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/BasicObjectExample.png)

>Method 1 and 2 do exactly the same thing. There is no need to use new Object().
>For simplicity, readability and execution speed, use the first one that is object literal.

### Object constructor
### 对象构造器

> Objects can be created using the constructor function using the following two steps:
>	1. Define the object type by writing the constructor function. 
	   By convention, name of the constructor function starts with a capital letter
>	2. Create an instance of the object with new 

可按如下步骤使用构造器函数来生成对象：
> 1. 通过构造器函数来定义对象类型(type not class).
     编码习惯上通常约定，构造器函数名的首字母大写．
> 2. 使用new运算符生成对象实例

>To define an object type, create a function for the object type that specifies its name, properties, and methods. 
>Lets create constructor function for the human type object

```javascript
function Human(firstName, lastName){
	this.firstName = firstName,
	this.lastName = lastName,
	this.fullName = function(){
		return this.firstName + " " + this.lastName;
	}
}
```

>Now you can create as many objects as you want using this constructor function:

```javascript
var viratKohli = new Human("Virat", "Kohli");
console.log(viratKohli);
```
#### Console output

![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/ObjectUsingConstructorFn.png)

```javascript
var sachinTendulkar = new Human("Sachin", "Tendulkar");
console.log(sachinTendulkar);
```

#### Console output

![alt text](https://github.com/rupeshmi/CodeSprint/blob/dev/JavaScript/Part1/CodeSnippets/SachinConst.png)

#### Delete a property from an object
#### 删除对象上的属性

>To delete a property from an object we can use the ‘delete’ operator. You cannot delete properties that were inherited, nor can you 
>delete properties with their attributes set to configurable.

可以使用`delete`操作符来删除对象上的属性.不可删除继承而来的属性,带有`DontDelete`属性的属性也不能被删除.

>***‘delete’*** operator returns true if the delete was successful. It also return true if the property to delete was non-existent or 
>the property could not be deleted

如果删除成功`delete`操作返回true, 如果要删除的属性不存在,或属性不可删除,同样返回true(很奇葩的设定)

```javascript
delete human.firstName; // return true
console.log(human);
```

> Let's see what happens if we try to call fullName method which uses both the firstName and lastName property of human object

在上述删除操作之后,再调用human的fullName方法,将会输出`undefined`,因为在fullName方法中试图访问了不存在的属性

```javascript
console.log(human.fullName());// undefined Kohli
```

>Output is **undefined** because we were trying to access firstName property of human object which does not exists
