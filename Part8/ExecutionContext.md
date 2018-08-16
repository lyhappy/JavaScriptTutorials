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


