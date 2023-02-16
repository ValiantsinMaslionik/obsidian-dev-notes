#react

---

React strict mode helps us write better React components by carrying out certain checks. This includes checks on class component life cycle methods. 
React components can either be implemented using a class or a function. Class components have special methods called life cycle methods that can execute logic at certain times in the component's life cycle.

- Strict mode checks that the life cycle methods will function correctly in React concurrent mode.
> React concurrent mode is a set of features that help React apps stay responsive, even when network speeds are slow. More information on concurrent mode can be found at https://reactjs.org/docs/concurrentmode-intro.html.
- Strict mode checks life cycle methods in third-party libraries, as well as the life cycle methods we have written. So, even if we build our app using function components, we may still get warnings about problematic life cycle methods.
- Strict mode checks also warn about usage of old APIs, such as the old context API. We will learn about the recommended context API in Chapter 12, Interacting with RESTful APIs.
- The last category of checks that strict mode performs are checks for unexpected side effects. Memory leaks and invalid application state are also covered in these checks.

> ![[zICO - Warning - 16.png]] Strict mode checks only happen in development mode – they do not impact a production build.

Strict mode can be turned on by using a `StrictMode` component from React. CRA has already enabled strict mode for the entirety of our app in *index.tsx*:
```jsx
ReactDOM.render(
	<React.StrictMode>
		<App />
	</React.StrictMode>,
	document.getElementById('root')
);
```

The `StrictMode` component is wrapped around all the React components in the component tree that will be checked. So, the `StrictMode` component is usually placed right at the top of the component tree.

Let's temporarily add usage of an old API to *App.tsx*. If the frontend project isn't open in Visual Studio Code, open it and carry out the following steps:
1. Add the following code at the bottom of *App.tsx*:
```jsx
class ProblemComponent extends React.Component {
	render() {
		return <div ref="div" />;
	}
}
```

This is a class component that uses an old *refs API* in React. *Refs* is short for references but is more often referred to as *refs* within the React community. Don't worry about fully understanding the syntax of this component – the key point is that it uses an API, which isn't recommended.

> A React ref is a feature that allows us to access the DOM node. More information on React refs can be found at https://reactjs.org/docs/refs-and-the-dom.html.

2. Reference this component in the App component:
```jsx
<div className="App">
	<header className="App-header">
		<ProblemComponent />
		…
	</header>
</div>
```
3. Start the application by running the following command in the Terminal: 
`npm start`
5. Open the browser console; the strict mode warning  will be displayed. Strict mode has output a warning to the console about an old API being used.
5. Remove `ProblemComponent` and the reference to it from *App.tsx*.
6. Press `Ctrl+C` and press `Y` when prompted to stop the app from running.

Now that we have a good understanding of strict mode, we are going to start creating the components for the home page in our app.
