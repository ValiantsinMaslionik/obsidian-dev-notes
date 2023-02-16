#react

---

In this section, we're going to understand JSX, which we briefly touched on in Chapter 1, Understanding the ASP.NET 5 React Template. We already know that JSX isn't valid JavaScript and that we need a preprocessor step to convert it into JavaScript. We are going to use the **Babel REPL** to play with JSX to get an understanding of how it maps to JavaScript by carrying out the following steps:
1. Open a browser, go to https://babeljs.io/repl, and enter the following JSX in the left-hand pane:
```jsx
<span>Q and A</span>
```
The following appears in the right-hand pane, which is what our JSX has compiled down to:
```js
React.createElement("span", null, "Q and A");
```
2. We can see that it compiles down to a call to `React.createElement`, which has three parameters:
	- The element type, which can be an HTML tag name (such as `span`), a React component type, or a React fragment type.
	- An object containing the properties to be applied to the element.
	- The children of the element.
3. Let's expand our example by putting a `header` tag around our `span`:
```jsx
<header><span>Q and A</span></header>
```
4. This compiles down to two calls with `React.createElement`, with `span` being passed in as a child to the `header` element that's created:
```js
// Note that the format of the code snippet will be slightly different to the format shown in the Babel REPL. The preceding snippet is more readable and allows us to clearly see the nested `React.createElement` statements.
React.createElement(
	"header",
	null,
	React.createElement(
		"span",
		null,
		"Q and A"
	)
);
```
5. Let's change the `span` tag to an `anchor` tag and add an `href` attribute:
```jsx
<header><a href="/">Q and A</a></header>
```
6. In the compiled JavaScript, we can see that the nested `React.createElement` call has changed to have `"a"` passed in as the element type, along with a properties object containing `href` as the second parameter:
```js
React.createElement(
	"header",
	null,
	React.createElement(
		"a",
		{ href: "/" },
		"Q and A"
	)
);
```
7. This is starting to make sense, but so far, our JSX only contains HTML. Let's start to mix in some JavaScript. We'll do this by declaring and initializing a variable and referencing it inside the anchor tag:
```jsx
var appName = "Q and A";
<header><a href="/">{appName}</a></header>
```
We can see that this compiles to the following with the JavaScript code:
```js
// So, the `appName` variable is declared in the first statement, exactly how we defined it, and is passed in as the children parameter in the nested `React.createElement` call.
var appName = "Q and A";
React.createElement(
	"header",
	null,
	React.createElement(
		"a",
		{ href: "/" },
		appName
	)
);
```
8. The key point to note here is that **we can inject JavaScript into HTML in JSX by using curly braces**. To further illustrate this point, let's add the word "app" to the end of `appName`:
```jsx
const appName = "Q and A";
<header><a href="/">{appName + " app"}</a></header>
```
This compiles down to the following:
```js
var appName = "Q and A";
React.createElement(
	"header",
	null,
	React.createElement(
		"a",
		{ href: "/" },
		appName + " app"
	)
);
```

So, JSX can be thought of as HTML with JavaScript mixed in using curly braces. This makes it incredibly powerful since regular JavaScript can be used to conditionally render elements, as well as render elements in a loop.
