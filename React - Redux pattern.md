#react

---

#  Understanding the Redux pattern

**Redux** is a predictable state container that can be used in React apps. In this section, we'll start by going through the three principles in Redux before understanding the benefits of Redux and the situations it works well in. Then, we will dive into the core concepts so that we understand the terminology and the steps that happen as the state is updated. By doing this, we will be well equipped to implement Redux in our app.

## Principles

Let's take a look at the three principles of Redux:
- **Single source of truth**: This means that the whole application state is stored in a single object. In a real app, this object is likely to contain a complex tree of nested objects.
- **The state is read-only**: This means that the state can't be changed directly. In Redux, the only way to change the state is to dispatch what's called an action.
- **Changes are made with pure functions**: The functions that are responsible for changing the state are called **reducers**.

Redux shines when many components need access to the same data because the state and its interactions are stored in a single place. Having the state read-only and only updatable with a function makes the interactions easier to understand and debug. It is particularly useful when many components are interacting with the state and some of the interactions are asynchronous.

In the following sections, we'll dive into actions and reducers a little more, along with the thing that manages them, which is called a store.

## Key concepts

The whole state of the application lives inside what is called a **store**. The state is stored in a JavaScript object like the following one:
```js
{
	questions: {
		loading: false,
		unanswered: [
			{
				questionId: 1, title: ...
			}, {
				questionId: 2, title: ...
			}
		]
	}
}
```

In this example, the single object contains an array of unanswered questions, along with whether the questions are being fetched from a web API.

**The state won't contain any functions or setters or getters**. It's a simple JavaScript object. The store also orchestrates all the moving parts in Redux. This includes pushing actions through reducers to update the state.

So, the first thing that needs to happen in order to update the state in a store is to dispatch an **action**. An **action** is another simple JavaScript object like the one in the following code snippet:
```js
{ 
	type: 'GettingUnansweredQuestions' 
}
```

The `type` property determines the kind of action that needs to be performed. The `type` property is an important part of the action because the reducer won't know how to change the state without it. In the previous example, the action doesn't contain anything other than the `type` property. This is because the *reducer* doesn't need any more information in order to make changes to the *state* for this *action*. 

The following example is another *action*:
```jsx
{
	type: 'GotUnansweredQuestions',
	questions: [
		{
			questionId: 1, title: ...
		}, {
			questionId: 2, title: ...
		}
	]
}
```

This time, an additional bit of information is included in the action in a `questions` property. This additional information is needed by the *reducer* to make the change to the *state* for this kind of *action*.

**Reducers** are pure functions that make the actual state changes.

> A pure function always returns the same result for a given set of parameters. So, these functions don't depend on any variables outside the scope of the function that isn't passed into the function. Pure functions also don't change any variables outside the scope of the function.

The following is an example of a *reducer*:
```jsx
const questionsReducer = (state, action) => {
	switch (action.type) {
		case 'GettingUnansweredQuestions': {
			return {
				...state,
				loading: true
			};
		}
		case 'GotUnansweredQuestions': {
			return {
				...state,
				unanswered: action.questions,
				loading: false
			};
		}
	}
};
```

Here are some key points regarding *reducers*:
- Reducers take in two parameters for the current *state* and the *action* that is being performed.
- A `switch` statement is used on the *action* type and creates a **new *state* object** appropriately for each action type in each of its branches.
- To create the new *state*, we *spread* the current *state* into **a new object and then overwrite it with properties that have changed**.
- The new *state* is returned from the *reducer*.

You'll notice that the *actions* and *reducer* we have just seen didn't have TypeScript types. Obviously, we'll include the necessary types when we implement these in the following sections.

Figure 7.1 – How components interact with Redux to get and update a state
![[zIMG-react-redux-7.1.png]]

1. *Components* get a *state* from the *store*. 
2. *Components* update the *state* by **dispatching an *action*** that is fed into a *reducer* that updates the *state*. 
3. The *store* passes the new *state* to the *component* when it is updated.

Now that we have started to get an understanding of what Redux is, it's time to put this into practice in our app.

# Installing Redux

Before we can use Redux, we need to install it, along with the TypeScript types. Let's perform the following steps to install Redux:
1. If we haven't already done so, let's open our project in Visual Studio Code from where we left off in the previous chapter. We can install the core Redux library via the terminal with the following command:
`npm install redux`

> Note that the core Redux library contains TypeScript types within it, so there is no need for an additional install for these.

2. Now, let's install the React-specific bits for Redux in the terminal with the following command:
`npm install react-redux`

These bits allow us to connect our React components to the Redux store.

3. Let's also install the TypeScript types for React Redux:
`npm install @types/react-redux --save-dev`

With all the Redux bits now installed, we can start to build our Redux store.

# IMPORTANT NOTE

> ![[zICO - Exclamation - 16.png]] The pattern described below is obsolete. See Redux library [documentataion](https://redux.js.org/introduction/why-rtk-is-redux-today) for details.

# Creating the state

In this section, we are going to implement the type for the *state* object in our *store*, along with the initial value for the *state*. Perform the following steps to do so:

1. Create a new file called *Store.ts* in the src folder with the following import statement:
```ts
import { QuestionData } from './QuestionsData';
```
2. Let's create the TypeScript types for the *state* of our *store*:
```ts
interface QuestionsState {
	readonly loading: boolean;
	readonly unanswered: QuestionData[];
	readonly viewing: QuestionData | null;
	readonly searched: QuestionData[];
}

export interface AppState {
	readonly questions: QuestionsState;
}
```

So, our *store* is going to have a `questions` property that is an object containing the following properties:
- `loading`: Whether a server request is being made
- `unanswered`: An array containing unanswered questions
- `viewing`: The question the user is viewing
- `searched`: An array containing questions matched in the search

3. Let's define the initial state for the *store* so that it has an empty array of unanswered questions:
```ts
const initialQuestionState: QuestionsState = {
	loading: false,
	unanswered: [],
	viewing: null,
	searched: [],
};
```

So, we have defined the types for the state object and created the initial state object. 

> **We have made the state read-only by using the `readonly` keyword before the state property names.**

# Creating actions

Actions initiate changes to our store state. In this section, we are going to create functions that create all the actions in our store. We will start by understanding all the actions that will be required in our store.

## Understanding the actions in the store

The three processes that will interact with the store are as follows:
- Fetching and rendering the unanswered questions on the home page
- Fetching and rendering the question being viewed on the question page
- Searching questions and showing the matches on the search page

Each process comprises the following steps:
1. When the process starts, the store's `loading` state is set to `true`.
2. The request to the server is then made.
3. When the response from the server is received, the data is put into the appropriate place in the store's state and `loading` is set to `false`.

Each process has two state changes. This means that each process requires two actions:
1. An action representing the start of the process
2. An action representing the end of the process, which will contain the data from the server request

So, our store will have six actions in total.

## Getting unanswered questions

We are going to create the actions in *Store.ts*. 

Let's create the two actions for the process that gets unanswered questions. Perform the following steps:
1. Let's start by creating a constant to hold the action type for our first action, which is indicating that unanswered questions are being fetched from the server:
```ts
export const GETTINGUNANSWEREDQUESTIONS = 'GettingUnansweredQuestions';
```

2. Create a function that returns this action:
```ts
export const gettingUnansweredQuestionsAction = () =>
({
	type: GETTINGUNANSWEREDQUESTIONS,
} as const);
```

Notice the as `const` keywords after the object is being returned. This is a TypeScript **const assertion**.

> A **const assertion** on an object will give it an immutable type. It also will result in string properties having a narrow string literal type rather than the wider string type.

The type of this action *without the const assertion* would be as follows:
```ts
{
	type: string
}
```

The type of this action *with the type assertion* is as follows:
```ts
{
	readonly type: 'GettingUnansweredQuestions'
}
```

So, the `type` property can only be *'GettingUnansweredQuestions'* and no other string value because we have typed it to that specific string literal. Also, the `type` property value can't be changed because it is read-only.

3. Create a function that returns the *action* for when the unanswered questions have been retrieved from the server:
```ts
export const GOTUNANSWEREDQUESTIONS =
	'GotUnansweredQuestions';

export const gotUnansweredQuestionsAction = (questions: QuestionData[],) =>
({
	type: GOTUNANSWEREDQUESTIONS,
	questions: questions,
} as const);
```

This time, the action contains a property called `questions` to hold the unanswered questions, as well as the fixed `type` property. We are expecting the questions to be passed into the function in the `questions` parameter.
That completes the implementation of the action types for getting unanswered questions.

## Viewing a question

Let's add the two actions for viewing a question using a similar approach:
```ts
export const GETTINGQUESTION = 'GettingQuestion';
export const gettingQuestionAction = () =>
({
	type: GETTINGQUESTION,
} as const);

export const GOTQUESTION = 'GotQuestion';
export const gotQuestionAction = (question: QuestionData | null,) =>
({
	type: GOTQUESTION,
	question: question,
} as const);
```

Notice that the action `type` property is given a *unique value*. This is required so that the *reducer* can determine what changes to make to the store's *state*.
We also make sure that the `type` property is given a value that is meaningful. This helps the readability of the code.

The data returned from the server can be a question or can be `null` if the question isn't found. **This is why a union type is used**.

## Searching questions

The final actions in the store are for searching questions. Let's add these now: 
```ts
export const SEARCHINGQUESTIONS = 'SearchingQuestions';
export const searchingQuestionsAction = () =>
({
	type: SEARCHINGQUESTIONS,
} as const);

export const SEARCHEDQUESTIONS = 'SearchedQuestions';
export const searchedQuestionsAction = (questions: QuestionData[],) =>
({
	type: SEARCHEDQUESTIONS,
	questions,
} as const);
```

The action types are again given unique and meaningful values. The data returned from the server search is an array of questions.

In summary, we have used the `Action` type from Redux to create interfaces for our six actions. This ensures that the action contains the required type property.
Our Redux store is shaping up nicely now. Let's move on and create a reducer.

# Creating a reducer

A *reducer* is a function that will make the necessary changes to the *state*. It takes in the current state and the action being processed as parameters and returns the new state. In this section, we are going to implement a reducer. 

Let's perform the following steps:
1. One of the parameters in the reducer is the action that invoked the state change. Let's create a union type containing all the action types that will represent the reducer action parameter:
```ts
type QuestionsActions =
| ReturnType<typeof gettingUnansweredQuestionsAction>
| ReturnType<typeof gotUnansweredQuestionsAction>
| ReturnType<typeof gettingQuestionAction>
| ReturnType<typeof gotQuestionAction>
| ReturnType<typeof searchingQuestionsAction>
| ReturnType<typeof searchedQuestionsAction>;
```

We have used the `ReturnType` utility type to get the return type of the action functions. `ReturnType` expects a function type to be passed into it, so we use the `typeof` keyword to get the type of each function.

> When `typeof` is used for a type, TypeScript will infer the type from the variable after the `typeof` keyword.

2. Next, create the skeleton reducer function:
```ts
const questionsReducer = (
	state = initialQuestionState,
	action: QuestionsActions
) => {
	// TODO - Handle the different actions and return
	// new state
	return state;
};
```

The reducer takes in two parameters, one for the current state and another for the action that is being processed. 
**The state will be undefined the first time the reducer is called**, so we  default this to the initial state we created earlier.

The reducer needs to return the new state object for the given action. We're simply returning the initial state at the moment.

> ![[zICO - Warning - 16.png]] It is important that a reducer always returns a value because a store may have multiple reducers. In this case, all the reducers are called, but won't necessarily process the action.

3. Now, add a switch statement to handle the different actions:
```ts
const questionsReducer = (
	state = initialQuestionState,
	action: QuestionsActions,
) => {
	switch (action.type) {
		case GETTINGUNANSWEREDQUESTIONS: {
		}
		break;
		case GOTUNANSWEREDQUESTIONS: {
		}
		break;
		case GETTINGQUESTION: {
		}
		break;
		case GOTQUESTION: {
		}
		break;
		case SEARCHINGQUESTIONS: {
		}
		break;
		case SEARCHEDQUESTIONS: {
		}
		break;
	}
	return state;
};
```

Notice that the `type` property in the action parameter is strongly typed and that we can only handle the six actions we defined earlier.

Let's handle the `GettingUnansweredQuestions` question first:
```ts
case GETTINGUNANSWEREDQUESTIONS: {
	return {
		...state,
		loading: true,
	};
}
```

We use the *spread syntax* to copy the previous state into a new object and then set the loading state to `true`.

> The **spread syntax** allows an object to expand into a place where key-value pairs are expected. The syntax consists of three dots followed by the object to be expanded. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax.

The spread syntax is commonly used in reducers to copy old state into the new state object without mutating the state passed into the reducer. This is important because the reducer must be a pure function and not change values outside its scope.

4. Let's now move on to the `GotUnansweredQuestions` action:
```ts
case GOTUNANSWEREDQUESTIONS: {
	return {
		...state,
		unanswered: action.questions,
		loading: false,
	};
}
```
We use the spread syntax to copy the previous state into a new object and set the unanswered and loading properties. Notice how we get IntelliSense only for the properties in the `GotUnansweredQuestions` action. TypeScript has smartly narrowed down the type in the switch branch from the union type that was passed into the reducer for the action parameter.

5. Handle the action getting a question by using the same approach:
```ts
case GETTINGQUESTION: {
	return {
		...state,
		viewing: null,
		loading: true,
	};
}
```

The question being viewed is reset to `null` and the loading state is set to `true` while the server request is being made.

6. Handle the action for receiving a question:
```ts
case GOTQUESTION: {
	return {
		...state,
		viewing: action.question,
		loading: false,
	};
}
```

The question being viewed is set to the question from the action and the loading state is reset to `false`.

7. Handle the action for searching questions:
```ts
case SEARCHINGQUESTIONS: {
	return {
		...state,
		searched: [],
		loading: true,
	};
}
```

The search results are initialized to an empty array and the loading state is set to `true` while the server request is being made.

8. Let's handle the last action, which is for receiving matched questions from the search:
```ts
case SEARCHEDQUESTIONS: {
	return {
		...state,
		searched: action.questions,
		loading: false,
	};
}
```

That's the reducer complete. We used a `switch` statement to handle the different action types. Within the switch branches, we used the *spread syntax* to copy the previous state and update the relevant values.
Now, we have all the different pieces implemented for our Redux store, so we are going to create a function to create the store in the next section.

# Creating the store

The final task in *Store.ts* is to create a function that creates the Redux store so that it can be provided to the React components. We need to feed all the store reducers into this function as well. 

Let's do this by performing the following steps:
1. First, let's import the `Store` type and the `createStore` and `combineReducers` functions from Redux:
```ts
import { Store, createStore, combineReducers } from 'redux';
```

- `Store` is the top-level type representing the Redux store. 
- We will use the `createStore` function to create the store later.
- `combineReducers` is a function we can use to put multiple reducers together into a format required by the `createStore` function.

2. Let's use the `combineReducers` function to create what is called a **root reducer**:
```ts
const rootReducer = combineReducers<AppState>({
	questions: questionsReducer
});
```

An object literal is passed into `combineReducers`, which contains the properties in our app state, along with the reducer that is responsible for that state. We only have a single property in our app state called `questions`, and a single reducer managing changes to that state called `questionsReducer`.

3. Create a function to create the store:
```ts
export function configureStore(): Store<AppState> {
	const store = createStore(rootReducer, undefined);
	return store;
}
```

- This function uses the `createStore` function from Redux by passing in the combined reducers and `undefined` as the initial state.
- We use the generic `Store` type as the return type for the function passing in the interface for our app state, which is `AppState`.

That's all we need to do to create the store.

> We have created all the bits and pieces in our store in a single file called *Store.ts*. For larger stores, it may help maintainability to structure the store across different files. Structuring the store by feature where you have all the actions and the reducer for each feature in a file works well because we generally read and write our code by feature.

In the next section, we will connect our store to the components we implemented in the previous chapters.

# Connecting components to the store

In this section, we are going to connect the existing components in our app to our store. We will start by adding what is called a *store provider* to the root of our component tree, which allows components lower in the tree to consume the store. We will then connect the *home*, *question*, and *search* pages to the Redux store using hooks from React Redux.

## Adding a store provider

Let's provide the store to the root of our component tree. To do that, perform the following steps:
1. In *App.tsx*, import the `Provider` component from React Redux and the `configureStore` function we created in the previous section. Add these import statements just after the React import statement:
```ts
import React from 'react';
import { Provider } from 'react-redux';
import { configureStore } from './Store';
```
This is the first time we have referenced anything from React-Redux. Remember that this library helps React components interact with a Redux store.

2. Just before the App component is defined, create an instance of our store using our `configureStore` function:
```tsx
const store = configureStore();
function App() {
...
}
```

3. In the `App` component's JSX, wrap a `Provider` component around the `BrowserRouter` component by passing in our store instance:
```tsx
return (
	<Provider store={store}>
		<BrowserRouter>
			...
		</BrowserRouter>
	</Provider>
);
```

Components lower in the component tree can now connect to the store.

## Connecting the home page

Let's connect the home page to the store. To do that, perform the following steps:
1. In *HomePage.tsx*, let's add the following `import` statement:
```tsx
import { useSelector, useDispatch } from 'react-redux';
```

We will eventually use the useSelector function to get state from the store. The `useDispatch` function will be used to invoke actions.

2. We are going to use the Redux store for the unanswered questions, so let's import the action functions for this, along with the type for the store's state:
```tsx
import {
	gettingUnansweredQuestionsAction,
	gotUnansweredQuestionsAction,
	AppState,
} from './Store';
```

3. Let's remove the `QuestionData` type from the `import` statement from *QuestionsData.ts* so that it now looks like the following:
```tsx
import {
	getUnansweredQuestions,
} from './QuestionsData';
```
The `QuestionData` type will be inferred in our revised implementation.

4. The `useDispatch` hook from React Redux returns a function that we can use to dispatch actions. Let's assign this to a function called `dispatch`:
```tsx
export const HomePage = () => {
	const dispatch = useDispatch();
	...
}
```

5. The `useSelector` hook from React Redux returns state from the store if we pass it a function that selects the state. Use the `useSelector` hook to get the unanswered questions state from the store:
```tsx
export const HomePage = () => {
	const dispatch = useDispatch();
	const questions = useSelector((state: AppState) =>
		state.questions.unanswered,
	);
	...
}
```
The function passed to `useSelector` is often referred to as a **selector**. 

> **Selector** takes in the store's state object and contains logic to return the required part of the state from the store.

6. Use the `useSelector` hook again to get the loading state from the store:
```tsx
const questions = useSelector(
	(state: AppState) => state.questions.unanswered,
);
const questionsLoading = useSelector(
	(state: AppState) => state.questions.loading,
);
```

7. Our local component state is now redundant, so let's remove it by deleting the highlighted lines:
```tsx
const questionsLoading = useSelector(
	(state: AppState) => state.questions.loading,
);

/*
const [
	questions,
	setQuestions,
] = React.useState<QuestionData[]>([]);

const [
	questionsLoading,
	setQuestionsLoading,
] = React.useState(true);
*/
```
8. Invoke the action for getting unanswered questions at the start of the `useEffect` function:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
		dispatch(gettingUnansweredQuestionsAction());
		const unansweredQuestions = await getUnansweredQuestions();
		setQuestions(unansweredQuestions);
		setQuestionsLoading(false);
	};
	doGetUnansweredQuestions();
}, []);
```
We dispatch the action to the store using the dispatch function.

9. Invoke the action for receiving unanswered questions after the call to `getUnansweredQuestions`:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
		dispatch(gettingUnansweredQuestionsAction());
		const unansweredQuestions = await getUnansweredQuestions();
		dispatch(gotUnansweredQuestionsAction(unansweredQuestions));
		setQuestions(unansweredQuestions);
		setQuestionsLoading(false);
	};
	doGetUnansweredQuestions();
}, []);
```

We pass in the unanswered questions to the function that creates this action. We then dispatch the action using the `dispatch` function.

10. Remove the references that set the local state from the `useEffect` function by removing the highlighted lines:
```tsx
React.useEffect(() => {
	const doGetUnansweredQuestions = async () => {
		…
		/**
		setQuestions(unansweredQuestions);
		setQuestionsLoading(false);
		*/
	};
	doGetUnansweredQuestions();
}, []);
```

11. ESLint is warning us that the `dispatch` function might be a missing dependency in the `useEffect` hook. We only want the `useEffect` function to trigger when the component is first mounted and not subsequently if the reference to the dispatch function changes. So, let's suppress this warning by adding the following line:
```tsx
React.useEffect(() => {
	…
	// eslint-disable-next-line react hooks/exhaustive-deps
}, []);
```

12. If the app isn't running, type `npm start` in the terminal to start it. The app will run fine and the unanswered questions will be rendered on the home page, just as they were before we added the Redux store.

Congratulations! We have just connected our first component to a Redux store!

The key parts of connecting a component to the store are using the `useSelector` hook to select the required state and using the useDispatch hook to dispatch actions.

## Connecting the question page

Let's connect the question page to the store. To do that, perform the following steps:
1. In QuestionPage.tsx, let's add the following import statements to import the
hooks we need from React Redux and action functions from our store:
import {
useSelector,
useDispatch,
} from 'react-redux';
import {
AppState,
gettingQuestionAction,
gotQuestionAction,
} from './Store';
2. Remove the QuestionData type from the import statement from
QuestionsData.ts so that it now looks like the following:
import { getQuestion, postAnswer } from './
QuestionsData';
3. Assign a dispatch function to the useDispatch hook:
export const QuestionPage = () => {
const dispatch = useDispatch();
...
}
4. Use the useSelector hook with a selector to get the question being viewed from
the state from the store:
const dispatch = useDispatch();
const question = useSelector(
(state: AppState) => state.questions.viewing,
);
5. Our local question state is now redundant, so let's remove it by removing the
highlighted lines:
const [
question,
setQuestion,
] = React.useState<QuestionData | null>(null);
Connecting components to the store 235
6. Inside the useEffect function, remove the reference to the local question state
and dispatch the appropriate actions from the store:
React.useEffect(() => {
const doGetQuestion = async (
questionId: number,
) => {
dispatch(gettingQuestionAction());
const foundQuestion = await
getQuestion(questionId);
dispatch(gotQuestionAction(foundQuestion));
};
if (questionId) {
doGetQuestion(Number(questionId));
}
// eslint-disable-next-line react
hooks/exhaustive-deps
}, [questionId]);
7. In the running app, browse to the question page as follows:
Figure 7.4 – QuestionPage component connected to the Redux store
The page will render correctly.
The question page is now connected to the store. Next, we'll connect our final component
to the store.
236 Managing State with Redux
Connecting the search page
Let's connect the search page to the store. To do that, perform the following steps:
1. In SearchPage.tsx, let's add the following import statements to import the
hooks we need from React Redux and types from our store:
import { useSelector, useDispatch } from 'react-redux';
import {
AppState,
searchingQuestionsAction,
searchedQuestionsAction,
} from './Store';
2. Remove the QuestionData type from the import statement from
QuestionsData.ts: so that it now looks like the following:
import { searchQuestions } from './QuestionsData';
3. Assign a dispatch function to the useDispatch hook:
export const SearchPage = () => {
const dispatch = useDispatch();
...
}
4. Use the useSelector hook with a selector to get the searched questions state
from the store:
const dispatch = useDispatch();
const questions = useSelector(
(state: AppState) => state.questions.searched,
);
5. Our local questions state is now redundant, so let's remove it by removing the
highlighted lines:
const [
questions,
setQuestions,
] = React.useState<QuestionData[] >([]);
Connecting components to the store 237
6. Inside the useEffect function, remove the reference to the local question state
and dispatch the appropriate actions from the store:
React.useEffect(() => {
const doSearch = async (criteria: string) => {
dispatch(searchingQuestionsAction());
const foundResults = await searchQuestions(
criteria,
);
dispatch(searchedQuestionsAction(foundResults));
};
doSearch(search);
// eslint-disable-next-line react
hooks/exhaustive-deps
}, [search]);
7. In the running app, perform a search operation by entering typescript, as
shown in the following screenshot:
Figure 7.5 – SearchPage component connected to the Redux store
The page will render correctly.
The search page is now connected to the store.
That completes connecting the required components to our Redux store.
We accessed the Redux store state by using the useSelector hook from React Redux.
We passed a function into this that retrieved the appropriate piece of state we needed in
the React component.
To begin the process of a state change, we invoked an action using a function returned
from the useDispatch hook from React Redux. We passed the relevant action object
into this function, containing information to make the state change.
238 Managing State with Redux
Notice that we didn't change the JSX in any of the connected components because we used
the same state variable names. We have simply moved this state to the Redux store.
Summary
In this chapter, we learned that the state in a Redux store is stored in a single place, is
read-only, and is changed with a pure function called a reducer. Our components don't
talk directly to the reducer; instead, they dispatch objects called actions that describe the
change to the reducer. We now know how to create a strongly typed type Redux store
containing a read-only state object with the necessary reducer functions.
We learned that React components can access a Redux store if they are children of
a Redux Provider component. We also know how to get state from the store from
a component using the useSelector hook and create a dispatcher to dispatch actions
with useDispatch as the hook.
There are lots of bits and pieces to get our heads around when implementing Redux within
a React app. It does shine in scenarios where the state management is complex because
Redux forces us to break the logic up into separate pieces that are easy to understand
and maintain. It is also very useful for managing global state, such as user information,
because it is easily accessible below the Provider component.
Now, we have built the majority of the frontend in our app, which means it's time to turn
our attention to the backend. In the next chapter, we'll focus on how we can interact with
the database in ASP.NET.
Questions
Before we end this chapter, let's test our knowledge with some questions:
1. When implementing an action object, how many properties can it contain?
2. How did we make the state in our store read-only?
3. Does the Provider component from React Redux need to be placed at the top of
the component tree?
4. What hook from React Redux allows a component to select the state from the
Redux store?
5. What is wrong with the following code that dispatches an action to the store?
useDispatch(gettingQuestionAction);
6. Is a component that consumes the Redux store allowed to have a local state?
Answers 239
Answers
1. An action can contain as many properties as we like! It needs to include at least
one for the type property. It can then include as many other properties as we need
for the reducer to change the state, but this is generally lumped into one additional
property usually called payload. So, generally, an action will have one or two
properties.
2. We used the readonly keyword in the properties in the interface for the state to
make it read-only.
3. The Provider component needs to be placed above the components that need
access to the store. So, it doesn't need to be right at the top of the tree.
4. The useSelector hook allows a component to select state from the store.
5. The useDispatch hook returns a function that can be used to dispatch an action
– it can't be used to dispatch an action directly. Here's the correct way to dispatch
an action:
const dispatch = useDispatch();
...
dispatch(gettingQuestionAction);
6. Yes, a component can have a local state as well as a Redux state. If the state is not
useful outside the component, then it is perfectly acceptable to have this state locally
within the component.
Further reading
Here are some useful links so that you can learn more about the topics that were covered
in this chapter:
• Getting started with Redux: https://redux.js.org/introduction/
getting-started
• React Redux: https://react-redux.js.org/
• Never type: https://www.typescriptlang.org/docs/handbook/
basic-types.html

Section 3:
Building an
ASP.NET Ba