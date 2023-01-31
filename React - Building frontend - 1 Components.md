#react

---

In this section, we are going to start by creating a component for the header of our app, which will contain our app name and the ability to search for questions. Then, we'll implement some components so that we can start to build the home page of the app, along
with some mock data.

# Creating a Header component

We can create a basic `Header` component and reference it within our `App` component by carrying out the following steps:
1. Create a new file called *Header.tsx* in the *src* folder.
2. Import React into the file with the following import statement:
```JSX
// We need to import React because, as we learned at the start of this chapter, JSX is transpiled into JavaScript `React.createElement` statements. 
// So, without React, these statements will error out.
import React from 'react';
```
3. Our component is just going to render the word header initially. So, enter the following as our initial `Header` component:
```JSX
export const Header = () => <div>header</div>;
```

The preceding component is actually an *arrow function* that is set to the `Header` variable.

> An arrow function is an alternative function syntax that was introduced in ES6. The arrow function syntax is a little shorter than the original syntax and it also preserves the lexical scope of this. The function parameters are defined in parentheses and the code that the function executes follows a `=>`, which is often referred to as a **fat arrow**. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions.

Notice that there are no curly braces or a `return` keyword. Instead, we just define the JSX that the function should return directly after the fat arrow. This is called an **implicit return**.
We use the `const` keyword to declare and initialize the `Header` variable.

> The `const` keyword can be used to declare and initialize a variable where its reference won't change later in the program. Alternatively, the `let` keyword can be used to declare a variable whose reference can change later in the program. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const.

We can now use the `Header` component within the `App` component.
1. The `export` keyword allows the component to be used in other files. So, let's use this in our `App` component by importing it into *App.tsx*. Add the following import statement beneath the other import statements in *App.tsx*:
```JSX
import { Header } from './Header';
```
2. Now, we can reference the `Header` component in the `App` component's return statement. Let's replace the header tag that CRA created for us with our `Header` component. Let's remove the redundant logo import as well:
```jsx
import React from 'react';
import './App.css';
import { Header } from './Header';

function App() {
	return (
		<div className="App">
			<Header />
		</div>
	);
};

export default App;
```
3. In the Visual Studio Code Terminal, enter `npm start` to run the app. We'll see that the word "header" appears at the top of the page

So, the arrow function syntax is a really nice way of implementing function-based components. The implicit return feature reduces the number of characters we need to type in. We'll use arrow functions with implicit returns heavily throughout this book.

## Adding elements to the Header component

So, the Header component will contain the app name, which will be Q & A, a search input, and a Sign In link.

With the app still running, carry out the following steps to modify the Header component:
1. Add the app name inside an anchor tag inside the `div` tag by replacing the word "header", which was previously used inside `div`:
```jsx
export const Header = () => (
	<div>
		<a href="./">Q & A</a>
	</div>
);
```

Notice that the implicit return statement containing the JSX is now in parentheses. 

> ![[zICO - Warning - 16.png]] When an implicit return statement is on multiple lines, parentheses are required. When an implicit return is on just a single line, we can get away without the parentheses.

Prettier automatically adds parentheses to an implicit return if they are needed, so we don't need to worry about remembering this rule.

2. Add an input to allow the user to perform a search:
```jsx
<div>
	<a href="./">Q & A</a>
	<input type="text" placeholder="Search..." />
</div>
```
3. Add a link to allow users to sign in:
```jsx
<div>
	<a href="./">Q & A</a>
	<input type="text" placeholder="Search..." />
	<a href="./signin"><span>Sign In</span></a>
</div>
```

4. The Sign In link needs a user icon next to it. We're going to use *user.svg* for this, which should already be in our project.

> *user.svg* was in the starter project for this chapter. You can download it from https://github.com/PacktPublishing/ASP.NET-Core-5-and-React-Second-Edition/tree/master/chapter-03/start/frontend/src/user.svg if you didn't start this chapter with the start project.

5. We are going to create a component to host this icon, so create a file called *Icons.tsx* in the *src* folder and enter the following content into it:
```jsx
import React from 'react';
import user from './user.svg';

export const UserIcon = () => (
	<img src={user} alt="User" width="12px" />
);
```

Here, we have created a component called `UserIcon` that renders an `img` tag, with the `src` attribute set to the `svg` file we imported from *user.svg*.

6. Let's go back to *Header.tsx* and import the icon component we just created:
```jsx
import { UserIcon } from './Icons';
```
7. Now, we can place an instance of the `UserIcon` component in the `Header` component inside button, before `span`:
```jsx
export const Header = () => (
	<div>
		<a href="./">Q & A</a>
		<input type="text" placeholder="Search..." />
		<a href="./signin">
			<UserIcon />
			<span>Sign In</span>
		</a>
	</div>
);
```

Our header doesn't look great, but we can see the elements in the `Header` component we just created. We'll style our `Header` component in the next chapter, Chapter 4, Styling React Components with Emotion.

# Creating a HomePage component

Let's create another component to get more familiar with this process. This time, we'll create a component for the home page by carrying out the following steps:
1. Create a file called *HomePage.tsx* in the src folder with the following content:
```jsx
import React from 'react';
export const HomePage = () => (
	<div>
		<div>
			<h2>Unanswered Questions</h2>
			<button>Ask a question</button>
		</div>
</div>
);
```
Our home page simply consists of a title containing the text, "Unanswered Questions", and a button to submit a question.
2. Open *App.tsx* and import our `HomePage` component:
```jsx
import { HomePage } from './HomePage';
```
3. Now, we can add an instance of `HomePage` under the Header component in the render method:
```jsx
<div className="App">
	<Header />
	<HomePage />
</div>
```

We have made a good start on the `HomePage` component. In the next section, we will create some mock data that will be used within it.














Implementing component state
Components can use what is called state to have the component re-render when a variable
in the component changes. This is crucial for implementing interactive components. For
example, when filling out a form, if there is a problem with a field value, we can use state
to render information about that problem. State can also be used to implement behavior
when external things interact with a component, such as a web API. We are going to do
this in this section after changing the getUnansweredQuestions function in order to
simulate a web API call.
Changing getUnansweredQuestions so that
it's asynchronous
The getUnansweredQuestions function doesn't simulate a web API call very
well because it isn't asynchronous. In this section, we are going to change this.
Follow these steps to do so:
1. Open QuestionsData.ts and create an asynchronous wait function that we
can use in our getUnansweredQuestions function:
const wait = (ms: number): Promise<void> => {
return new Promise(resolve => setTimeout(resolve, ms));
};
This function will wait asynchronously for the number of milliseconds we pass
into it. The function uses the native JavaScript setTimeout function internally so
that it returns after the specified number of milliseconds. Notice that the function
returns a Promise object.
Important Note
A promise is a JavaScript object that represents the eventual completion
(or failure) of an asynchronous operation and its resulting value.
The Promise type in TypeScript is like the Task type in .NET. More
information can be found at https://developer.mozilla.
org/en-US/docs/Web/JavaScript/Reference/Global_
Objects/Promise.
Implementing component state 107
Notice <void> after the Promise type in the return type annotation. Angle
brackets after a TypeScript type indicate that this is a generic type.
Important Note
Generic types are a mechanism for allowing the consumer's own type
to be used in the internal implementation of the generic type. The angle
brackets allow the consumer type to be passed in as a parameter. Generics in
TypeScript is very much like generics in .NET. More information can be found
at https://www.typescriptlang.org/docs/handbook/
generics.html.
We are passing a void type into the generic Promise type. But what is
the void type?
The void type is another TypeScript-specific type that is used to represent a
non-returning function. So, void in TypeScript is like void in .NET.
2. Now, we can use the wait function in our getUnansweredQuestions function
to wait half a second:
export const getUnansweredQuestions = async ():
Promise<QuestionData[]> => {
await wait(500);
return questions.filter(q => q.answers.length ===
0);
};
Notice the await keyword before the call to the wait function and
the async keyword before the function signature.
async and await are two JavaScript keywords we can use to make asynchronous
code read almost identically to synchronous code. await stops the next line from
executing until the asynchronous statement has completed, while async simply
indicates that the function contains asynchronous statements. So, these keywords
are very much like async and await in .NET.
We return Promise<QuestionData[]> rather
than QuestionData[] because the function doesn't return the questions straight
away. Instead, it returns the questions eventually.
108 Getting Started with React and TypeScript
3. So, the getUnansweredQuestions function is now asynchronous. If we
open HomePage.tsx, which is where this function is consumed, we'll see
a compilation error:
Figure 3.10 – Type error on the data prop
This is because the return type of the function has changed and no longer
matches what we defined in the QuestionList props interface.
4. For now, let's comment the instance of QuestionList out so that our app
compiles:
{/* <QuestionList data={getUnansweredQuestions()} /> */}
Important Note
Lines of code can be commented out in Visual Studio Code by highlighting the
lines and pressing Ctrl + / (forward slash).
Eventually, we're going to change HomePage so that we can store the questions in the
local state and then use this value in the local state to pass to QuestionList. To do this,
we need to invoke getUnansweredQuestions when the component is first rendered
and set the value that's returned to state. We'll do this in the next section.
Using useEffect to execute logic
So, how do we execute logic when a function-based component is rendered? Well, we can
use a useEffect hook in React, which is what we are going to do in the following steps:
1. We need to change HomePage so that it has an explicit return statement since we
want to write some JavaScript logic in the component, as well as return JSX:
export const HomePage = () => {
return (
<Page>
...
</Page>
Implementing component state 109
);
};
2. Now, we can call the useEffect hook before we return the JSX:
export const HomePage = () => {
React.useEffect(() => {
console.log('first rendered');
}, []);
return (
...
);
};
Important Note
The useEffect hook is a function that allows a side effect, such as fetching
data, to be performed in a component. The function takes in two parameters,
with the first parameter being a function to execute. The second parameter
determines when the function in the first parameter should be executed.
This is defined in an array of variables that, if changed, results in the first
parameter function being executed. If the array is empty, then the function is
only executed once the component has been rendered for the first time. More
information can be found at https://reactjs.org/docs/hookseffect.
html.
So, we output first rendered into the console when the HomePage component is
first rendered.
3. In the running app, let's open the browser developer tools and inspect the console:
Figure 3.11 – useEffect being executed
So, our code is executed when the component is first rendered, which is great.
Note that we shouldn't worry about the ESLint warnings about the
unused QuestionList component and the getUnansweredQuestions variable.
This is because these will be used when we uncomment the reference to
the QuestionList component.
110 Getting Started with React and TypeScript
Using useState to implement component state
The time has come to implement state in the HomePage component so that we can
store any unanswered questions. But how do we do this in function-based components?
Well, the answer is to use another React hook called useState. Follow the steps listed
in HomePage.tsx to do this:
1. Add the QuestionData interface to the QuestionsData import:
import {
getUnansweredQuestions,
QuestionData
} from './QuestionsData';
2. We'll use this hook just above the useEffect statement in
the HomePage component to declare the state variable:
const [
questions,
setQuestions,
] = React.useState<QuestionData[]>([]);
React.useEffect(() => {
console.log('first rendered');
}, []);
Important Note
The useState function returns an array containing the state variable in
the first element and a function to set the state in the second element. The
initial value of the state variable is passed into the function as a parameter. The
TypeScript type for the state variable can be passed to the function as a generic
type parameter. More information can be found at https://reactjs.
org/docs/hooks-state.html.
Notice that we have destructured the array that's returned from useState into
a state variable called questions, which is initially an empty array, and a function
to set the state called setQuestions. We can destructure arrays to unpack their
contents, just like we did previously with objects.
So, the type of the questions state variable is an array of QuestionData.
Implementing component state 111
3. Let's add a second piece of state called questionsLoading to
indicate whether the questions are being fetched:
const [
questions,
setQuestions,
] = React.useState<QuestionData[]>([]);
const [
questionsLoading,
setQuestionsLoading,
] = React.useState(true);
We have initialized this state to true because the questions are being fetched
immediately in the first rendering cycle. Notice that we haven't passed a type into
the generic parameter. This is because, in this case, TypeScript can cleverly infer
that this is a boolean state from the default value, true, that we passed into
the useState parameter.
4. Now, we need to set these pieces of state when we fetch the unanswered questions.
First, we need to call the getUnansweredQuestions function asynchronously
in the useEffect hook. Let's add this and remove the console.log statement:
React.useEffect(() => {
const questions = await getUnansweredQuestions();
},[]);
We immediately get a compilation error:
Figure 3.12 – useEffect error
5. This error has occurred because the useEffect function callback isn't flagged
as async. So, let's try to make it async:
React.useEffect(async () => {
const questions = await getUnansweredQuestions();
}, []);
112 Getting Started with React and TypeScript
6. Unfortunately, we get another error:
Figure 3.13 – Another useEffect error
Unfortunately, we can't specify an asynchronous callback in
the useEffect parameter.
7. The error message guides us to a solution. We can create a function that calls
getUnansweredQuestions asynchronously and call this function within the
useEffect callback function:
React.useEffect(() => {
const doGetUnansweredQuestions = async () => {
const unansweredQuestions = await
getUnansweredQuestions();
};
doGetUnansweredQuestions();
}, []);
8. Now, we need to set the questions and questionsLoading states once we
have retrieved the data:
useEffect(() => {
const doGetUnansweredQuestions = async () => {
const unansweredQuestions = await
getUnansweredQuestions();
setQuestions(unansweredQuestions);
setQuestionsLoading(false);
};
doGetUnansweredQuestions();
}, []);
Implementing component state 113
9. In the HomePage JSX, we can uncomment the QuestionList reference and pass
in our question state:
<Page>
<div ... >
...
</div>
<QuestionList data={questions} />
</Page>
If we look at the running app, we'll see that the questions are being rendered nicely
again.
10. We haven't made use of the questionsLoading state yet. So, let's change
the HomePage JSX to the following:
<Page>
<div>
...
</div>
{questionsLoading ? (
<div>Loading…</div>
) : (
<QuestionList data={questions || []} />
)}
</Page>
Here, we are rendering a Loading... message while the questions are being fetched.
Our home page will render nicely again in the running app, and we should see
a Loading... message while the questions are being fetched.
11. Before we move on, let's take some time to understand when components are
re-rendered. Still in HomePage.tsx, let's add a console.log statement before
the return statement and comment out useEffect:
// React.useEffect(() => {
// ...
// }, []);
console.log('rendered');
return ...
114 Getting Started with React and TypeScript
Every time the HomePage component is rendered, we'll see a rendered message in
the console:
Figure 3.14 – Rendered twice with no state changes
So, the component is rendered twice when no state is set.
In development mode, components are rendered twice if in strict mode and if the
component contains state. This is so that React can detect unexpected side effects.
12. Comment useEffect back in but leave one of the state setter functions
commented out:
React.useEffect(() => {
const doGetUnansweredQuestions = async () => {
const unansweredQuestions = await
getUnansweredQuestions();
setQuestions(unansweredQuestions);
// setQuestionsLoading(false);
};
doGetUnansweredQuestions();
}, []);
The component is rendered four times:
Figure 3.15 – Rendered four times with a state change
React renders a component when state is changed and because we are in strict
mode, we get a double render.
We get a double render when the component first loads and a double render after
the state change. So, we get four renders in total.
13. Comment the other state setter back in:
React.useEffect(() => {
const doGetUnansweredQuestions = async () => {
const unansweredQuestions = await
getUnansweredQuestions();
setQuestions(unansweredQuestions);
Implementing component state 115
setQuestionsLoading(false);
};
doGetUnansweredQuestions();
}, []);
The component is rendered six times:
Figure 3.16 – Rendered six times when two pieces of state are changed
14. Let's remove the console.log statement before continuing.
So, we are starting to understand how we can use state to control what is rendered when
external things such as users or a web API interact with components. A key point that we
need to take away is that when we change state in a component, React will automatically
re-render the component.
Important Note
The HomePage component is what is called a container component,
with QuestionList and Question being presentational components.
Container components are responsible for how things work, fetching any
data from a web API, and managing state. Presentational components are
responsible for how things look. Presentational components receive data via
their props, and also have property event handlers so that their containers can
manage user interactions.
Structuring a React app into a container and presentational components often allows
presentation components to be used in different scenarios. Later in this book, we'll see
that we can easily reuse QuestionList on other pages in our app.
In the next section, we are going to learn how to implement logic when users interact with
components using events.
116 Getting Started with React and TypeScript
Handling events
JavaScript events are invoked when a user interacts with a web app. For example, when a
user clicks a button, a click event will be raised from that button. We can implement a
JavaScript function to execute some logic when the event is raised. This function is often
referred to as an event listener.
Important Note
In JavaScript, event listeners are attached to an element using
its addEventListener method and removed using
its removeEventListener method.
React allows us to declaratively attach events in JSX using function props, without the
need to use addEventListener and removeEventListener. In this section, we are
going to implement a couple of event listeners in React.
Handling a button click event
In this section, we are going to implement an event listener on the Ask a question button
in the HomePage component. Follow these steps to do so:
1. Open HomePage.tsx and add a click event listener to the button element in
the JSX:
<button onClick={handleAskQuestionClick}>
Ask a question
</button>
Important Note
Event listeners in JSX can be attached using a function prop that is named
with on before the native JavaScript event name in camel case. So, a
native click event can be attached using an onClick function prop. React
will automatically remove the event listener for us before the element is
destroyed.
Handling events 117
2. Let's implement the handleAskQuestionClick function, just above
the return statement in the HomePage component:
const handleAskQuestionClick = () => {
console.log('TODO - move to the AskPage');
};
return ...
3. If we click on the Ask a question button in the running app, we'll see the following
message in the console:
Figure 3.17 – Click event
So, handling events in React is super easy! In Chapter 5, Routing with React Router, we'll
finish off the implementation of the handleAskQuestionClick function and navigate
to the page where a question can be asked.
Handling an input change event
In this section, we are going to handle the change event on the input element
and interact with the event parameter in the event listener. Follow these steps to do so:
1. Open Header.tsx and add a change event listener to the input element
in the JSX:
<input
type="text"
placeholder="Search..."
onChange={handleSearchInputChange}
/>
118 Getting Started with React and TypeScript
2. Let's change the Header component so that it has an explicit return statement
and implement the handleSearchInputChange function just above it:
export const Header = () => {
const handleSearchInputChange = (
e: React.ChangeEvent<HTMLInputElement>
) => {
console.log(e.currentTarget.value);
};
return ( ... );
};
3. Notice the type annotation, React.ChangeEvent<HTMLInputElement>,
for the event parameter. This ensures the interactions with the event parameter are
strongly typed.
4. If we type something into the search box in the running app, we'll see each change
in the console:
Figure 3.18 – Change event
In this section, we've learned that we can implement strongly typed event listeners, which
will help us avoid making mistakes when using the event parameter. We'll finish off the
implementation of the search input in Chapter 6, Working with Forms.
Summary
In this chapter, we learned that JSX compiles JavaScript nested calls
into createElement functions in React, which allows us to mix HTML and JavaScript.
Questions 119
We learned that we can create a React component using functions with strongly typed
props passed in as parameters. Now, we know that a prop can be a function, which is how
events are handled.
The component state is used to implement behavior when users or other external things
interact with it. Due to this, we understand that a component and its children are
re-rendered when the state is changed.
By completing this chapter, we understand that React can help us discover problems in
our app when it is run in strict mode. We also understand that a component is double
rendered in strict mode when it contains state.
In the next chapter, we are going to style the home page.
Questions
Try to answer the following questions to test your knowledge of this chapter:
1. Does a component re-render when its props change?
2. How can we create some state called rating that is initially set to 0?
3. What function prop name would we use to add a keydown event listener?
4. A component has the following props interface:
interface Props {
name: string;
active: boolean;
}
How can we destructure the Props parameter and default active to true?
5. How can we use the useEffect hook to call a synchronous function
called getItems when a piece of state called category changes while we're
passing category to getItems?
Answers
1. Yes, when the props of a component change, it will rerender.
2. The state can be defined as follows:
const [rating, setRating] = React.useState(0);
3. We would use the onKeyDown prop to handle the keydown event.
120 Getting Started with React and TypeScript
4. We could destructure the props parameter and set active to true, as follows:
export const myComponent = ({name, active = true}: Props)
=> …
5. The useEffect hook would be