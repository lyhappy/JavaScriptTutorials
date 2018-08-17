# Execution Context, Scope chain and JavaScript internals

> **Execution context (EC)** is defined as the environment in which JavaScript code is executed.
> By environment I mean the value of this, variables, objects, and functions JavaScript code has access to, constitutes it’s environment.

**执行上下文(EC)** 是指JavaScript代码的执行环境.
所谓环境是指`this`的值,变量,对象,以及JS代码访问的函数等组成的环境.

> Execution context in JavaScript are of three types:

JS的执行上下文共分三种:

* **Global execution context (GEC):** This is the default execution context in which JS code start it’s execution when the file first loads in the browser. All the global code are executed inside global execution context. In the browser context, if the code is executing in strict mode value of this is undefined else it is window object. Global execution context cannot be more than one because only one global environment is possible for JS code execution.
* **Functional execution context (FEC):** Functional execution context is defined as the context created by the execution of code inside a function. Each function has it’s own execution context. It can be more than one. Functional execution context have access to all the code of global execution context. While executing global execution context code, if JS engine finds a function call, it creates a new functional execution context for that function.
* **Eval:** Execution context inside eval function.

* **全局执行上下文(GEC):** 这是浏览器加载完JS文件,开始执行JS代码时的默认执行上下文.所有的全局代码都在全局上下文执行.在浏览器环境中,如果是严格模式下执行,this的值为undefined,否则为window对象.全局上下文在JS执行环境中只有一个.
* **函数执行上下文(FEC):** 函数执行上下文是函数代码执行时创建的.每个函数都有自己的执行上下文.JS代码执行过程中,可能存在多个函数执行上下文.函数执行上下文可以访问全局执行上下文中的所有代码(变量和函数).在执行全局执行上下文中的代码时,如果JS引擎遇到了函数定义,将创建爱你一个新的函数执行上下文.
* **Eval:** eval函数的执行上下文.

> Execution context stack (ECS): Execution context stack is a stack data structure to store all the execution stacks created while executing the JS code. Global execution context is present by default in execution context stack and it is at the bottom of the stack. While executing global execution context code, if JS engines finds a function call, it creates functional execution context of that function and pushes that function execution context on top of execution context stack. JS engine executes the function whose execution context is at the top of the execution context stack. Once all the code of the function is executed, JS engines pop’s out that function’s execution context and start’s executing the function which is below it.

**执行上下文栈(ECS):** 执行上下文栈是一个栈数据结构，用来存储JS代码执行过程中的所有执行上下文.
GEC默认出现在ECS中，且在栈的底部.当运行GEC中的代码是，如果JS引擎遇到了函数调用，它将创建一个用于该函数的FEC，
并将其压入ECS中。JS引擎总是在执行ECS栈中顶部的FEC对应的函数.当函数执行结束时，JS引擎会将对应的FEC弹出栈，并继续执行栈里的下个函数。

> Let’s understand this with the help of an example:

看个例子：
```JavaScript
var a = 10;

function functionA() {
  console.log("Start function A");
  function functionB(){
    console.log("In function B");
  }
  functionB();
}

functionA();
console.log("GlobalContext");
```

![](./CodeSnippets/ECS.gif)

> As soon as the above code loads into the browser, JS engine pushes global execution context in the execution context stack. When functionA is called from global execution context, JS engines pushes functionA execution context in the execution context stack and starts executing functionA.

如上代码一旦加载进浏览器，JS引擎就会将GEC压入ECS中。当`functionA`在GEC中被调用时，JS引擎再次将`functionA`的FEC压入ECS，并开始执行`functionA`.

> When functionB is called from functionA execution context, JS engine pushes functionB execution context in the execution context stack. Once all the code of functionB gets executed, JS engines pops out functionB execution context. As after this, functionA execution context is on top of the execution context stack, JS engine starts executing remaining code of functionA.

当`functionB`在`functionA`的FEC中被调用时，JS引擎同样会将`functionB`的FEC压入ECS。一旦`functionB`的所有代码都执行完毕，JS引擎会弹出`functionB`的FEC。
这之后，`functionA`的FEC又出现在ECS的顶部，JS引擎继续执行`functionA`的剩余代码。

> Once all the code from functionA gets executed, JS engines pops out functionA execution context from execution context stack and starts executing remaining code from the global execution context.

一旦`functionA`中的左右代码都执行完毕，JS引擎弹出`functionA`的FEC并继续执行GEC中的剩余代码。

> When all the code is executed JS engines pops out the global execution context and execution of JavaScript ends.

当所有代码都执行完时，JS引擎弹出GEC，即JS代码的执行结束了。

> So far we have discussed how JavaScript engine handles the execution context. Now, we will see how JavaScript engine creates the execution context.

到这里，我们讨论了JS引擎如何处理执行上下文。现在，我们继续看看JS引擎是如何创建执行上下文的。

> JavaScript engine creates the execution context in the following two stages:

* Creation phase
* Execution phase

JS引擎创建执行上下文要经历如下两个阶段：

* 创建阶段
* 执行阶段

> Creation phase is the phase in which JS engines has called a function but it’s execution has not started. In the creation phase, JS engine is in the compilation phase and it scans over the function to compile the code.

创建阶段是指JS引擎已调用一个函数，但还没开始执行它。在创建阶段，JS引擎处于编译状态，它在扫描函数的代码并编译之。

> In creation phase, JS engine performs the following task:

* **Creates the Activation object or the variable object**: Activation object is a special object in JS which contain all the variables, function arguments and inner functions declarations information. As activation object is a special object it does not have the dunder proto property.
* **Creates the scope chain:** Once the activation object gets created, JS engine initializes the scope chain which is a list of all the variables objects inside which the current function exists. This also includes the variable object of global execution context. Scope chain also contains the current function variable object.
* **Determines the value of this:** After the scope chain, JavaScript engine initialize the value of ‘this’.

在创建阶段，JS引擎完成以下任务：

* **创建活动对象(Activation object)或变量对象(Variable object)：** 活动对象是JS中的一种特殊对象，它包含了所有变量，函数参数，内部函数的声明信息。由于其特殊性，所以没有dunder proto属性。
* **创建作用域链(Scope chain)：** 活动对象创建结束后，JS引擎开始初始化作用域链，这是包含当前函数所有变量的一个列表，同时也包含了GEC的变量。
* **决定this的值：** 上一步之后，JS引擎开始初始化this的值。

> Let’s understand how JavaScript engines creates the activation object with an example

用一个例子来理解这些任务：

```JavaScript
function funA (a, b) {
  var c = 3;
  var d = 2;

  d = function() {
    return a - b;
  }
}

funA(3, 2);
```

> Just after funA is called and before code execution of funA starts, JS engine creates an executionContextObj for funcA which can be represented as shown below:

在`funA`被调用但还未开始执行前，JS引擎创建了一个如下的`executionContextObj`:

```json
executionContextObj = {
  variableObject: {}, // All the variable, arguments and inner function details of the funA
  scopechain: [], // List of all the scopes inside which the current function is
  this // Value of this
}
```

> Activation object or variable object contains argument object which have details about the arguments of the function.

活动对象或变量对象包含参数对象，参数对象详细描述了函数接收的参数。

> It will have a property name for each of the variables and functions which are declared inside the current function

当前函数内定义的所有变量和内部函数都以属性的方式出现在变量对象（variableObject）中。

> Activation object or the variable object in our case will be as shown below:

在我们的例子中，活动对象或变量对象的结构如下：

``` JavaScript
variableObject = {
  argumentObject : {
    0: a,
    1: b,
    length: 2
  },
  a: 3,
  b: 2
  c: undefined,
  d: undefined then pointer to the function defintion of d
}
```

1. ArgumentObject: JS engines will create the argument object as shown in the above code. It will also have the length property indicating the total number of arguments in the function.
2. Now, for each variable in the function, JS engine will create a property on the Activation object and will initialize it with undefined. As arguments are also variables inside the function, they are also present as a property of the argument object.
3. If the variable already exists as a property of the argument object JS engine will not do anything and will move to the next line.
4. When JS engines encounters a function definition inside the current function, JS engine will create a new property by the name of the function. Function definitions are stored in heap memory, they are not stored in the execution context stack. Function name property points to it’s definition in the heap memory.

1. **argumentObject**: JS引擎会按如上代码所示来创建参数对象。同时还会在argumentObject上添加一个length属性来表示函数的参数个数。
2. 现在，针对函数内定义的每一个变量，JS引擎将在variableObject上添加一个属性，并初始为undefined。因为参数也属于函数内部的变量，所以再次出现在这里。
3. 如果变量已经以参数对象的属性的形式存在的话，JS引擎将不会做任何事而是直接到下一行代码。
4. 当JS引擎在当前函数内遇到函数定义时，JS引擎会以函数名为属性名而在variableObject上添加一个属性。函数定义存储在堆内存中，而不在ECS中，以函数名为名的属性将指向堆内存中的函数定义。

> Hence, first d will get the value of undefined as it is a variable but when JS engine encounters a function with the same name it overrides it’s value to point it to the definition of function d stored in the heap.

所以，第一次d被声明为一个变量时得到的值为`undefined`,但当JS引擎解析到函数声明时，函数表达式赋值给了d，所以d又指向了堆中存储的函数定义。

> After this JS engines will create the scope chain and will determine the value of this.

这之后JS引擎将生成作用域链，并决定this的值。

### Execution phase:

### 执行阶段

> In the execution phase, JS engines will again scan through the function to update the variable object with the values of the variables and will execute the code.

在执行阶段，JS引擎将再次扫描整个函数代码，并更新变量对象的初始值，然后执行代码。

> After the execution stage, variable object will look like this:

执行阶段结束后，variableObject看起来是这样的：

```JavaScript
variableObject = {
  argumentObject : {
    0: a,
    1: b,
    length: 2
  },
  a: 3,
  b: 2,
  c: 3,
  d: undefined then pointer to the function defintion of d
}
```

### Complete example:
### 完成的例子:

> Consider the code below:

考虑下面的代码：

```JavaScript
a = 1;

var b = 2;

cFunc = function(e) {
  var c = 10;
  var d = 15;

  a = 3

  function dFunc() {
    var f = 5;
  }

  dFunc();
}

cFunc(10);
```

> When the above code loads in the browser, JS engine will enter the compilation phase to create the execution objects. In the compilation phase JS engine will handle only the declarations, it does not bother about the values. This is the creation phase of execution context.

浏览器加载完上述代码后，JS引擎将进入编译状态并创建执行上下文。在编译阶段，JS引擎只处理声明而不管初始值。
接下来详细看看执行上下文的创建阶段。

* **Line 1:** In the line variable a is assigned a value of 1, so JS engines does not think of it as a variable declaration or function declaration and it moves to line 3. It does not do anything with this line in compilation phase as it is not any declaration.

* **第一行:** 这一行给变量a赋值1，JS引擎不认为这是一个变量声明或函数声明语句，所以直接跳过到第3行。因为不是任何声明语句，引擎在编译阶段对这一行不做任何处理。

* **Line 3:** As the above code is in global scope and it’s a variable declaration, JS engines will create a property with the name of this variable in the global execution context object and will initialize it with undefined value.

* **第三行:** 因为上面的代码在全局作用域，且这一行在声明一个变量，JS引擎将以变量名为名在GEC对象上创建一个属性,并初始化其值为undefined.

* **Line 5:**  JS engine finds a function declaration, so it will store the function definition in a heap memory and create a property which will point to location where function definition is stored. JS engines doesn’t know what is inside of cFunc.

* **第五行:** JS引擎发现一个函数声明，它会将函数定义存储到堆内存，并添加一个属性指向函数定义处。JS引擎此刻并不管函数cFunc内部是如何定义的。

* **Line 18:** This code is not any declaration hence, JS engine will not do anything.

* **第18行:** 这行代码不是声明语句，所以JS引擎不做任何事。

#### Global Execution Context object after the compilation phase stage:
#### 编译阶段结束后的GEC对象：

``` JavaScript
globalExecutionContextObj = {
  activationbj: {
      argumentObj : {
          length:0
      },
      b: undefined,
      cFunc: Pointer to the function definition
  },
  scopeChain: [GLobal execution context variable object],
  this: value of this
}
```

> As further there is no code, JS engine will now enter the execution phase and will scan the function again. Here, it will update the variable value and will execute the code.

再往下没有代码了，所有JS引擎现在将进入执行阶段并再次扫描整个代码。这次将会更新变量的值并执行代码。

* **Line 1:** JS engines find that there is no property with name a in the variable object, hence it adds this property in the global execution context and initializes it’s value to 1.

* **第一行:** JS 引擎发现并没有一个名为a的属性在variableObject上, 所以它在GEC上添加了新属性a并初始化它的值为1.

* **Line 3:** JS engines checks that there is a property with name b in the variable object and hence update it’s value with 2.

* **第3行:** JS引擎检在活动变量上查到有一个属性名为b,然后更新它的值为2.

* **Line 5:** As it is a function declaration, it doesn’t do anything and moves to line 18.

* **第5行:** 因为这行是函数声明，所以引擎不做任何事并直接移植18行。

* **Line 18:** Here, cFunc is called, so JS engine again enters the compilation phase to create the execution context object of cFunc by scanning it.

* **第18行:** 这里调用了cFunc,JS引擎再次进入编译阶段并通过扫描函数代码创建一个cFunc函数的执行上下文对象

#### Global execution context object after the execution phase:
#### 执行阶段后的GEC对象:

``` JavaScript
globalExecutionContextObj = {
  activationbj: {
      argumentObj : {
          length:0
      },
      b: 2,
      cFunc: Pointer to the function definition,
      a: 1
  },
  scopeChain: [GLobal execution context variable object],
  this: value of this
}
```

> As cFunc has e as an argument, JS engine will add e in the argument object of cFunc execution context object and create a property by the name of e and will initialize it with 2.

`cFunc`函数有一个参数为`e`，JS引擎会将e添加到cFunc的执行上下文对象的argumentObject上，并初始化为2.

* **Line 6:** JS engine will check if c is a property in the activation object of cFunc. As there is no property by that name, it will add c as a property and will initialize with undefined value.

* **第6行:** JS引擎将会检查c是否是cFunc的活动对象上的一个属性。因为并没找到，所以引擎会创建属性c并初始化为undefined.

* **Line 7:** Same as line 6

* **第7行:** 和第6行一样

* **Line 9:** As this line is not a declaration, JS engine will move to next line

* **第9行:** 这一行不是声明，引擎将跳过

* **Line 11:** JS engine finds a function declaration, so it will store the function definition in the heap memory and create a property dFunc which will point to location where function definition is stored. JS engines doesn’t know what is inside dFunc.

* **第11行:** JS引擎又遇到一个函数声明，同样会将该函数的定义存储在堆内存中，并创建一个属性dFunc执行堆中的函数定义。引擎此刻不管dFunc的内部实现.

* **Line 15:** As this statement is not a declaration, JS engine will not do anything.

* **第15行:** 这行语句不是声明，引擎忽略。

#### cFunc execution context object after compilation phase:
#### 编译阶段结束后的cFunc 函数的执行上下文对象(FEC)

```JavaScript
cFuncExecutionContextObj = {
  activationbj: {
      argumentObj : {
          0: e,
          length:1
      },
      e: 10,
      c: undefined,
      d: undefined
      dFunc: Pointer to the function definition,
  },
  scopeChain: [cFunc variable object, Global exection context variable object],
  this: value of this
}
```

> As further there are no lines in this function JS engine will enter the execution phase and will execute cFunc by scanning it again.

cFunc中再无代码，JS引擎再次进入执行阶段并再次扫描执行代码。

* **Line 6 and 7:** c and d get’s the value of 10 and 15 respectively

* **第6和7行:** c 和 d 的值各自被初始化为10和15

* **Line 9:** As a is not a property on cFunc execution context object and it’s not a declaration, JS engine will move to the global execution context with the help of scope chain and checks if a property with name a exists in global execution context object. If the property does not exists, it will create a new one and will initialize it. Here, as property with a already exists on the global execution context object, it will updates it’s value to 3 from 1. JS engines moves to global execution context in this case only i.e. when it finds a variable in the execution phase which is not a property on the current execution context object

* **第9行:** a不是cFunc函数FEC对象上有记录的额属性，且这一行不是声明语句，引擎会继续在作用域链的帮助下，在GEC上继续查找属性a. 如果没找到，引擎会在GEC上创建一个新属性a并初始化它（非严格模式下）.这里，属性a已经存在于GEC上，引擎会更新它的值为3.
在这个例子中，当引擎在当前执行上下文对象中找不到某个变量的声明时，便会向上一级（此处是全局执行上下文）继续查找。

* **Line 11:** JS engines will create a dFunc property and will points to it’s heap location

* **第11行:** 引擎创建一个dFunc属性，并指向它在堆中的地址。

#### Execution context object of cFunc after the execution phase:
#### 执行阶段结束后的cFunc函数的FEC对象：

```JavaScript
cFuncExecutionContextObj = {
  activationbj: {
      argumentObj : {
          0: e,
          length:1
      },
      e: 10,
      c: 10,
      d: 15
      dFunc: Pointer to the function definition,
  },
  scopeChain: [cFunc variable object, Global exection context variable object],
  this: value of this
}
```

* **Line 15:** As this is a function call, JS engines will again enter the compilation phase to create dFunc execution context object.
* **第15行:** 函数调用，引擎再次进入编译阶段并创建dFunc的FEC对象。

> dFunc execution context object has access to all the variables and functions defined on cFunc and in the global scope using the scope chain.

dFunc函数的FEC对象通过作用域链可以访问cFunc和全局作用域中的所有的变量和函数定义.

> Similarly cFunc has access to all the variables and objects in the global scope but it does not have any access to the dFunc variables and objects.

同样,cFunc可以访问全局作用域中的所有变量和对象，但不能访问dFunc中的变量和对象。

> Global execution context does not have access to cFunc or dFunc variables or objects.

GEC不能访问cFunc和dFunc中的变量或对象。

> With the above concepts, I guess it’s will be easy to understand how hoisting works in JavaScript.

通过上面的概念，应该很容易理解JS中的hoisting.

### Scope Chain:
### 作用域链:

> Scope chain is a list of all the variable objects of functions inside which the current function exists. Scope chain also consists of the current function execution object.

作用域链是当前函数中的所有函数的variableObjects的列表.作用域链同时构成了当前函数的FEC对象。

#### Consider the below code:

```JavaScript
a = 1;

var b = 2;

cFunc = function(e) {
  var c = 10;
  var d = 15;

  console.log(c);
  console.log(a);

  function dFunc() {
    var f = 5;
    console.log(f)
    console.log(c);
    console.log(a);
  }

  dFunc();
}

cFunc(10);
```

> Here, when function cFunc is called from the global execution context, scope chain of cFunc will look like this

当函数cFunc在全局作用域被调用时，cFunc的作用域链看来如下：

```
Scope chain of cFunc = [  cFunc variable object,
                          Global Execution Context variable object ]
```

> When dFunc is called from cFunc, as dFunc is inside cFunc, dFunc’s scope chain consists of dFunc variable object, cFunc variable object and global execution context variable object.

当函数dFunc在cFunc中被调用时，因为dFunc在cFunc内部,dFunc的作用域链由dFunc函数内部的变量对象，cFunc函数内部的变量对象以及全局作用域下的变量对象组成。

```
Scope chain of dFunc = [  dFunc variable object,
                          cFunc variable object,
                          Global Execution Context variable object ]
```

> When we try to access f inside dFunc, JS engines checks if f is available inside dFunc’s variable object. If it finds f’s value it console.log f’s value.

当在dFunc中的访问f时，引擎检查到f在dFunc的变量对象中是可访问的。然后执行代码在控制台中输出f的值。

> When we try to access variable c inside dFunc, JS engines checks if c is available inside dFunc’s variable object. If the variable is not available, then it will move to cFunc variable object.

当在dFunc中访问c时，引擎会现在dFunc的变量对象中检查c是否可访问。如果没有找到，引擎将会继续在cFunc中查找。

> As variable c is not available inside dFunc’s variable object, JS engines moves to cFunc’s variable object. As c is available on cFunc variable object, it will console.log c’s value.

因为变量c在dFunc的变量对象中没有找到，引擎继续在cFunc的变量对象中查找，并找到了，然后执行代码在控制台中输出c的值。

> When we try to log a’s value inside dFunc, JS engines will check if a is available inside dFunc’s variable object. If a is not available inside dFunc’s variable object, it will move to the next item in scope chain i.e. cFunc’s variable object. JS engines will check if cFunc’s variable object as variable a. Here, variable a is not available on cFunc’s variable object hence, it will check the next items in dFunc’s scope chain i.e. global execution context variable object. Here a is available on dFunc’s variable object and it will console a’ s value.

当试图在dFunc函数中输出a的值时，引擎会依次在dFunc的变量对象，cFunc的变量对象以及全局执行上下文的变量对象中查找a，并最终在GEC的变量对象中找到，然后打印之。

> Similarly, in case of cFunc, JS engine will find variable a’s value from global execution object.

同样的，在cFunc中，引擎将在GEC的变量对象中找到变量a的声明。

> cFunc does not know that variable f exists. Hence if we try to access f from cFunc it will give error. But, dFunc function has access c and d variable using the scope chain

cFunc中无法访问变量f，但dFunc可以访问c和d

> Closures can be explained using the scope chain context in JavaScript.

闭包也可以用作用域链来解释。


译注：没有仔细研究过各浏览器的引擎实现，本文有关引擎对执行上下问和作用域链的操作，可理解为理想操作模型，实际各浏览器引擎的实现可能未必如此。
