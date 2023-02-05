#react

---

JavaScript events are invoked when a user interacts with a web app. For example, when a user clicks a button, a `click` event will be raised from that button. We can implement a JavaScript function to execute some logic when the event is raised. **This function is often referred to as an event listener**.

> In JavaScript, event listeners are attached to an element using its `addEventListener` method and removed using its `removeEventListener` method. 
> React allows us to declaratively attach events in JSX using function props, without the need to use `addEventListener` and `removeEventListener`. 

# Handling a button click event

In this section, we are going to implement an event listener on the *Ask a question* button in the `HomePage` component. Follow these steps to do so:
1. Open *HomePage.tsx* and add a click event listener to the button element in the JSX:
```jsx
<button onClick={handleAskQuestionClick}>
Ask a question
</button>
```

> Event listeners in JSX can be attached using a function prop that is named with `on` **before the native JavaScript event name in camel case**. So, a native `click` event can be attached using an `onClick` function prop. React will automatically remove the event listener for us before the element is destroyed.

2. Let's implement the `handleAskQuestionClick` function, just above the return statement in the `HomePage` component:
```jsx
const handleAskQuestionClick = () => {
	console.log('TODO - move to the AskPage');
};
return ...
```
3. If we click on the Ask a question button in the running app, we'll see the `TODO - move to the AskPage` message in the console.

So, handling events in React is super easy! In Chapter 5, Routing with React Router, we'll finish off the implementation of the `handleAskQuestionClick` function and navigate to the page where a question can be asked.

# Handling an input change event

In this section, we are going to handle the `change` event on the `input` element and interact with the `event` parameter in the event listener. Follow these steps to do so:
1. Open *Header.tsx* and add a `change` event listener to the input element in the JSX:
```jsx
<input
	type="text"
	placeholder="Search..."
	onChange={handleSearchInputChange}
/>
```

2. Let's change the `Header` component so that it has an explicit `return` statement and implement the `handleSearchInputChange` function just above it:
```jsx
export const Header = () => {
	const handleSearchInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
		console.log(e.currentTarget.value);
	};
	return ( ... );
};
```

3. Notice the type annotation, `React.ChangeEvent<HTMLInputElement>`, for the event parameter. This ensures the interactions with the event parameter are strongly typed.
4. If we type something into the search box in the running app, we'll see each change in the console.
