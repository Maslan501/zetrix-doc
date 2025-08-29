# Syntax

## Syntax in Zetrix smart contract

### Overview

Zetrix smart contracts are developed using **JavaScript**, and to promote secure and standardized coding practices, **JSLint** is integrated for syntax validation. This ensures contracts conform to a consistent code style and helps detect common programming errors before deployment.

#### **Syntax Validation with JSLint**

* **Purpose**: JSLint enforces a strict subset of JavaScript to improve contract safety, readability, and maintainability.
* **Standard Reference**: Zetrix adapts the core JSLint rules and augments them with custom modifications tailored for smart contract use.
* **Usage Documentation**: Detailed explanations of the standard JSLint syntax and Zetrix-specific extensions are provided through a local documentation site.

#### **Accessing the JSLint Manual**

You can access the complete JSLint guide, including examples and rule definitions, via:

```html
127.0.0.1:36002/jslint/help.html
```

This URL is accessible through **Zetrix node servers** or **wallet interfaces** that run the local environment.

> **Note**: Ensure the local node or wallet server is running to open this documentation.

This tool is essential for validating contract scripts prior to deployment on the Zetrix blockchain.

### Detection Tool

You can access the Zetrix JSLint interface via:

```html
127.0.0.1:36002/jslint/index.html
```

This page is available through **Zetrix node servers** or **local wallet interfaces**, and serves as the primary interface for contract syntax checking.

For error description, details will be given when you debug contract syntax in the web tool. When you input the following code:

```javascript
"use strict";
function init(bar)
{
    
}
```

Error is shown below:

```
Empty block.   2.0
{
```

Cause: Blank statement block at row 2 and column 0.

Correct code is shown below:

```javascript
"use strict";
function init(bar)
{
    return;    
}
```

If the code is correct, no warning or error messages will be shown the tool will simply remain silent, indicating successful validation..

### &#x20;<a href="#text-compression" id="text-compression"></a>

### Text Compression

After writing your smart contract, you can use the **JSMin** tool to compress the code. This reduces file size by removing unnecessary whitespace and comments. However, since compression is **irreversible** and may affect readability, it's important to **save the original version** of the contract before performing this operation.

### &#x20;<a href="#demo" id="demo"></a>

### Demo

```javascript
"use strict";
function init(bar)
{
    /*init whatever you want*/
    return;
}

function main(input) 
{
    log(input);

    //for statement
    let i;
    for (i = 0; i < 5; i += 1) 
    {
        log(i);
    }

    //while statement
    let b = 10;
    while (b !== 0) 
    {
        b -= 1;
        log(b);
    }

    //if statement
    let compare = 1;
    if(compare === 1)
    {
        log("it is one");
    }
    else if(compare === 2)
    {
        log("it is two");
    }
    else
    {
        log("it is other");
    }

    //if statement
    if(compare !== 2)
    {
        log("no, different");
    }

    //switch statement
    let sw_value = 1;
    switch(sw_value)
    {
    case 1:
        log("switch 1");
        break;
    default:
        log("default");
    }

    //Number
    let my_num = Number(111);
    log(my_num);

    //String
    let my_str = String(111);
    log(my_str);

    //Boolean
    let my_bool = Boolean(111);
    log(my_bool);

    //Array
    let str_array = ["red","black"]; 
    log(str_array);

    //Array
    let num_array = [1,2,3,4];
    log(num_array);

    throw "this is a exception";
}
```

### &#x20;<a href="#rules-list" id="rules-list"></a>

### Rules List

* **Strict Mode**
  *   Every contract must begin with:

      ```javascript
      javascriptCopyEdit"use strict";
      ```
* **Variable Declarations**
  * Use `let` to declare variables within block scopes.
  * Avoid `var` for variable declarations.
* **Comparison Operators**
  * Use strict equality: `===` and `!==`
  * Avoid loose comparisons: `==` and `!=`
* **Statement Termination**
  * All statements must end with a **semicolon (`;`)**.
* **Statement Blocks**
  * Blocks must be enclosed in `{}`.
  * **Empty blocks are not allowed**.
* **For Loops**
  * Declare the loop variable **before** the condition block.
  * Update its value with **explicit assignment**, not using `++` or `--`.
* **Operators**
  * Use `+=` and `-=` instead of `++` and `--`.
* **Restricted Keywords**
  * **Prohibited**: `eval`, `void`, `try`, and `catch`
  * You **can** use `throw` to raise exceptions.
* **Object Creation**
  * Do not use `new` with primitives like `Number`, `String`, or `Boolean`.\
    Example (not allowed): `new Number(5)`
  * Instead, use direct value constructors:\
    Example (allowed): `Number("5")`
* **Array Creation**
  * Avoid creating arrays using the `Array` constructor.\
    Example (not allowed): `new Array(3)`
  * Instead, use array literals:\
    Example (allowed): `[1, 2, 3]`
* **Prohibit to use keywords** like `try` and `catch`, but you can use `throw` to throw exceptions

```javascript
"Array", "ArrayBuffer", "Float32Array", "Float64Array", 
"Int8Array", "Int16Array", "Int32Array", "Uint8Array", 
"Uint8ClampedArray", "Uint16Array", "Uint32Array"

let color = new Array(100); //Compiling error

//You can use the alternative new Array(100) statement;
let color = ["red","black"]; 
let arr = [1,2,3,4];
```

* Keywords prohibited to use

```javascript
"DataView", "decodeURI", "decodeURIComponent", "encodeURI", 
"encodeURIComponent", "Generator","GeneratorFunction", "Intl", 
"Promise", "Proxy", "Reflect", "System", "URIError", "WeakMap", 
"WeakSet", "Math", "Date"
```
