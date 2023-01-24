#javascript 

---

[Local document](zDOC_javascript-var-let-const.mhtml)
https://www.scaler.com/topics/javascript/difference-between-var-let-and-const/

### Difference between Var, Let, and Const

The following table briefs the difference between let and var and const in javascript:

var|let|const
--|--|--
var has the function or global scope.|let's have the block scope.|const variable has the block scope.
It gets hoisted to the top of its scope and initialized undefined.|It also got hoisted to the top of its scope but didn't initialize.|It also got hoisted to the top of its scope but didn't initialize.
It can be updated or re-declared.|It can only be updated and can't be re-declared.|It can't be updated or re-declared.
It's an old way to declare a variable.|It's a new way to declare variables introduced in ES6.|It's also a new way to declare a variable, which introduces in ES6.

## Conclusion:

-   **var**, **let**, and **const** are keywords that allow us to declare variables.
-   Scope of a variable tells us where we can access this variable inside our code and where we can't. It is one of the decisive reasons for the difference between let and var and const in javascript.
-   **Hoisting** provides features to hoist our variables and function declaration to the top of its scope before code execution.
-   var is a nice and innocent way to declare a variable, which gets hoisted to the top of the scope.
-   let and const are the modern ways to declare variables, which also get hoisted but don't initialize.