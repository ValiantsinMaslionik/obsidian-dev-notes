#javascript

---

https://www.w3schools.com/js/js_this.asp

---

### Example

```js
const person = {  
  firstName: "John",  
  lastName : "Doe",  
  id       : 5566,  
  fullName : function() {  
    return this.firstName + " " + this.lastName;  
  }  
};
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_method)

---

## What is **this**?

In JavaScript, the `this` keyword refers to an **object**. **Which** object depends on how `this` is being invoked (used or called).

The `this` keyword refers to different objects depending on how it is used:
- In an object method, `this` refers to the **object**.
- Alone, `this` refers to the **global object**.
- In a function, `this` refers to the **global object**.
 - In a function, in strict mode, `this` is `undefined`.
- In an event, `this` refers to the **element** that received the event.

Methods like `call()`, `apply()`, and `bind()` can refer `this` to **any object**.

## **this** in a Method

When used in an object method, `this` refers to the **object**.
In the example on top of this page, `this` refers to the **person** object. Because the **fullName** method is a method of the **person** object.
```js
fullName : function() {  
  return this.firstName + " " + this.lastName;  
}
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_method)

## **this** Alone

When used alone, `this` refers to the **global object**. Because `this` is running in the global scope.
In a browser window the global object is `[object Window]`:

### Example
```js
let x = this;
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this)

In **strict mode**, when used alone, `this` also refers to the **global object**

### Example
```js
"use strict";  
let x = this;
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_alone)

## **this** in a Function (Default)

In a function, the **global object** is the default binding for `this`. 
In a browser window the global object is `[object Window]`:

### Example
```js
function myFunction() {  
  return this;  
}
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_function)

## **this** in a Function (Strict)

JavaScript **strict mode** does not allow default binding.
So, when used in a function, in strict mode, `this` is `undefined`.

### Example
```js
"use strict";  
function myFunction() {  
  return this;  
}
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_strict)

## **this** in Event Handlers

In HTML event handlers, `this` refers to the HTML element that received the event:

### Example
```html
<button onclick="this.style.display='none'">  
  Click to Remove Me!  
</button>
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_event)

## Object Method Binding

In these examples, `this` is the **person object**:

### Example
```js
const person = {  
  firstName  : "John",  
  lastName   : "Doe",  
  id         : 5566,  
  myFunction : function() {  
    return this;
  }  
};
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_object)

### Example
```js
const person = {  
  firstName: "John",  
  lastName : "Doe",  
  id       : 5566,  
  fullName : function() {  
    return this.firstName + " " + this.lastName;  
  }  
};
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_method)

i.e. **this.firstName** is the **firstName** property of **this** (the **person object**).

## Explicit Function Binding

The `call()` and `apply()` methods are predefined JavaScript methods.
They can both be used to call an object method with another object as argument.

## See Also:

[The Function call() Method](https://www.w3schools.com/js/js_function_call.asp)
[The Function apply() Method](https://www.w3schools.com/js/js_function_apply.asp)
[The Function bind() Method](https://www.w3schools.com/js/js_function_bind.asp)

The example below calls `person1.fullName` with `person2` as an argument, **this** refers to `person2`, even if `fullName` is a method of `person1`:

### Example
```js
const person1 = {  
  fullName: function() {  
    return this.firstName + " " + this.lastName;  
  }  
}  
  
const person2 = {  
  firstName:"John",  
  lastName: "Doe",  
}  
  
// Return "John Doe":  
person1.fullName.call(person2);
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_this_call)

## Function Borrowing

With the `bind()` method, an object can borrow a method from another object.
This example creates 2 objects (`person` and `member`).
The member object borrows the `fullname` method from the `person` object:

### Example
```js
const person = {  
  firstName:"John",  
  lastName: "Doe",  
  fullName: function () {  
    return this.firstName + " " + this.lastName;  
  }  
}  
  
const member = {  
  firstName:"Hege",  
  lastName: "Nilsen",  
}  
  
let fullName = person.fullName.bind(member);
```

[Try it Yourself »](https://www.w3schools.com/js/tryit.asp?filename=tryjs_function_bind_borrow)

## **This** Precedence

To determine which object `this` refers to; use the following precedence of order.

Precedence

Object

1

bind()

2

apply() and call()

3

Object method

4

Global scope

Is `this` in a function being called using bind()?

Is `this` in a function being called using apply()?

Is `this` in a function being called using call()?

Is `this` in an object function (method)?

Is `this` in a function in the global scope.