#react

---

Components can use what is called *state* **to have the component re-render when a variable in the component changes**. This is crucial for implementing interactive components. For example, when filling out a form, if there is a problem with a field value, we can use state
to render information about that problem. State can also be used to implement behavior when external things interact with a component, such as a web API. We are going to do this in this section after changing the getUnansweredQuestions function in order to simulate a web API call.

# Changing getUnansweredQuestions so that it's asynchronous

The `getUnansweredQuestions` function doesn't simulate a web API call very well because it isn't asynchronous. In this section, we are going to change this. Follow these steps to do so:
1. Open *QuestionsData.ts* and create an asynchronous `wait` function that we can use in our `getUnansweredQuestions` function:
```tsx
const wait = (ms: number): Promise<void> => {
	return new Promise(resolve => setTimeout(resolve, ms));
};
```

This function will wait asynchronously for the number of milliseconds we pass into it. The function uses the native JavaScript `setTimeout` function internally so that it returns after the specified number of milliseconds. Notice that the function returns a `Promise` object.

> A **promise** is a JavaScript object that represents the eventual completion (or failure) of an asynchronous operation and its resulting value. **The `Promise` type in TypeScript is like the `Task` type in .NET**. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/GlobalObjects/Promise.

Notice `<void>` after the `Promise` type in the return type annotation. Angle brackets after a TypeScript type indicate that this is a **generic type**. 

> *Generic types* are a mechanism for allowing the consumer's own type to be used in the internal implementation of the generic type. The angle brackets allow the consumer type to be passed in as a parameter. **Generics in TypeScript is very much like generics in .NET**. More information can be found at https://www.typescriptlang.org/docs/handbook/generics.html.

We are passing a `void` type into the generic Promise type. But what is the `void` type?
The `void` type is another TypeScript-specific type **that is used to represent a non-returning function**. **So, void in TypeScript is like void in .NET**.

2. Now, we can use the `wait` function in our `getUnansweredQuestions` function to wait half a second:
```jsx
export const getUnansweredQuestions = async ():

Promise<QuestionData[]> => {
	await wait(500);
	return questions.filter(q => q.answers.length === 0);
};
```

Notice the `await` keyword before the call to the wait function and the `async` keyword before the function signature. `async` and `await` are two JavaScript keywords we can use to make asynchronous code read almost identically to synchronous code. `await` stops the next line from executing until the asynchronous statement has completed, while `async` simply indicates that the function contains asynchronous statements. **So, these keywords are very much like `async` and `await` in .NET**.

We return `Promise<QuestionData[]>` rather than `QuestionData[]` because the function doesn't return the questions straight away. Instead, it returns the questions eventually.

3. So, the `getUnansweredQuestions` function is now asynchronous. If we open `HomePage.tsx`, which is where this function is consumed, we'll see a compilation error.
This is because the return type of the function has changed and no longer matches what we defined in the `QuestionList` props interface.

4. For now, let's comment the instance of `QuestionList` out so that our app compiles:
```tsx
{/* <QuestionList data={getUnansweredQuestions()} /> */}
```

> Lines of code can be commented out in Visual Studio Code by highlighting the lines and pressing `Ctrl + /`` (forward slash).

Eventually, we're going to change `HomePage` so that we can store the questions in the local state and then use this value in the local state to pass to `QuestionList`. To do this, we need to invoke `getUnansweredQuestions` when the component is first rendered and set the value that's returned to state. We'll do this in the next section.

## Using `useEffect` to execute logic

So, how do we execute logic when a function-based component is rendered? Well, we can use a `useEffect` hook in React, which is what we are going to do in the following steps:
1. We need to change `HomePage` so that it has an explicit return statement since we want to write some JavaScript logic in the component, as well as return JSX:
```jsx
export const HomePage = () => {
	return (
		<Page>
			...
		</Page>
	);
};
```

2. Now, we can call the `useEffect` hook before we return the JSX:
```jsx
export const HomePage = () => {
	React.useEffect(() => {
		console.log('first rendered');
	}, []);
	return (
	...
	);
};
```

> The `useEffect` hook is a function that **allows a side effect, such as fetching data**, to be performed in a component. The function takes in two parameters, 
> 	- The first parameter being a function to execute. 
> 	- The second parameter determines when the function in the first parameter should be executed. 
> This is defined in an array of variables that, **if changed, results in the first parameter function being executed**. If the array is empty, then the function is only executed once the component has been rendered for the first time. 
> More information can be found at https://reactjs.org/docs/hookseffect.html.

So, we output first rendered into the console when the HomePage component is first rendered.

Note that we shouldn't worry about the ESLint warnings about the unused `QuestionList` component and the `getUnansweredQuestions` variable. This is because these will be used when we uncomment the reference to the `QuestionList` component.

# Using useState to implement component state

The time has come to implement state in the `HomePage` component so that we can store any unanswered questions. 
But how do we do this in function-based components? Well, the answer is to use another React hook called `useState`. 

Follow the steps listed in *HomePage.tsx* to do this:
1. Add the `QuestionData` interface to the `QuestionsData` import:
```tsx
import {
	getUnansweredQuestions,
	QuestionData
} from './QuestionsData';
```

2. We'll use this hook just above the `useEffect` statement in the `HomePage` component to declare the state variable:
```tsx
const [
	questions,
	setQuestions,
] = React.useState<QuestionData[]>([]);

React.useEffect(() => {
	console.log('first rendered');
}, []);
```

> The `useState` function 
>	- returns **an array** containing the **state variable** in the first element, and a **function to set the state** in the second element. 
>	- the initial value of the state variable is passed into the function as a parameter. 
>	- the TypeScript type for the state variable can be passed to the function as a generic type parameter. 
> More information can be found at https://reactjs.org/docs/hooks-state.html.

Notice that we have destructured the array that's returned from `useState` into a state variable called `questions`, which is initially an empty array, and a function to set the state called `setQuestions`. 
We can destructure arrays to unpack their contents, just like we did previously with objects.
So, the type of the questions state variable is an array of `QuestionData`.

3. Let's add a second piece of state called `questionsLoading` to indicate whether the questions are being fetched:
```tsx
const [
	questions,
	setQuestions,
] = React.useState<QuestionData[]>([]);

const [
	questionsLoading,
	setQuestionsLoading,
] = React.useState(true);
```

We have initialized this state to `true` because the questions are being fetched immediately in the first rendering cycle. Notice that we haven't passed a type into the generic parameter. This is because, in this case, TypeScript can cleverly infer that this is a boolean state from the default value, true, that we passed into the `useState` parameter.

4. Now, we need to set these pieces of state when we fetch the unanswered questions.
First, we need to call the `getUnansweredQuestions` function asynchronously in the `useEffect` hook. Let's add this and remove the `console.log` statement:
```tsx
React.useEffect(() => {
	const questions = await getUnansweredQuestions();
},[]);
```
We immediately get a compilation error.

5. This error has occurred because the useEffect function callback isn't flagged as async. So, let's try to make it async:
```tsx
React.useEffect(async () => {
	const questions = await getUnansweredQuestions();
}, []);
```

6. Unfortunately, we get another error: we can't specify an asynchronous callback in the `useEffect` parameter.
7. The error message guides us to a solution. We can create a function that calls `getUnansweredQuestions` asynchronously and call this function within the `useEffect` callback function:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
		const unansweredQuestions = await getUnansweredQuestions();
	};
	doGetUnansweredQuestions();
}, []);
```
8. Now, we need to set the questions and questionsLoading states once we have retrieved the data:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
		const unansweredQuestions = await getUnansweredQuestions();
		setQuestions(unansweredQuestions);
		setQuestionsLoading(false);
	};
	doGetUnansweredQuestions();
}, []);
```

9. In the `HomePage` JSX, we can uncomment the `QuestionList` reference and pass in our question state:
```tsx
<Page>
	<div ... >
	...
	</div>
	<QuestionList data={questions} />
</Page>
```

If we look at the running app, we'll see that the questions are being rendered nicely again.

10. We haven't made use of the `questionsLoading` state yet. So, let's change the `HomePage` JSX to the following:
```tsx
<Page>
	<div>
	...
	</div>
	{questionsLoading ? (<div>Loading…</div>) : (<QuestionList data={questions || []} />)}
</Page>
```

Our home page will render nicely again in the running app, and we should see a *Loading...* message while the questions are being fetched.

11. Before we move on, let's take some time to understand when components are re-rendered. Still in *HomePage.tsx*, let's add a `console.log` statement before the `return` statement and comment out `useEffect`:
```tsx
// React.useEffect(() => {
// ...
// }, []);
console.log('rendered');
return ...
```

Every time the HomePage component is rendered, we'll see a rendered message in the console.

**So, the component is rendered twice when no state is set.**

> ![[zICO - Warning - 16.png]] In **development mode**, components are **rendered twice if** in strict mode and if the component contains state. This is so that React can detect unexpected side effects.

12. Comment `useEffect` back in but leave one of the state setter functions commented out:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
	const unansweredQuestions = await getUnansweredQuestions();
		setQuestions(unansweredQuestions);
		// setQuestionsLoading(false);
	};
	doGetUnansweredQuestions();
}, []);
```

The component is rendered four times.
React renders a component when state is changed and because we are in strict mode, we get a double render. We get a double render when the component first loads and a double render after the state change. So, we get four renders in total.

13. Comment the other state setter back in:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
		const unansweredQuestions = await getUnansweredQuestions();
		setQuestions(unansweredQuestions);
		setQuestionsLoading(false);
	};
	doGetUnansweredQuestions();
}, []);
```

The component is rendered four times (??).

14. Let's remove the `console.log` statement before continuing.

So, we are starting to understand how we can use state to control what is rendered when external things such as users or a web API interact with components. 
**A key point that we need to take away is that when we change state in a component, React will automatically re-render the component**.

The `HomePage` component is what is called a **container component,** with `QuestionList` and `Question` being **presentational components**. 
	- *Container components* are responsible for *how things work*, fetching any data from a web API, and managing state. 
	- *Presentational components* are responsible for *how things look*. Presentational components receive data via their props, and also have property event handlers so that their containers can manage user interactions.

Structuring a React app into a container and presentational components often allows presentation components to be used in different scenarios. Later in this book, we'll see that we can easily reuse `QuestionList` on other pages in our app.
