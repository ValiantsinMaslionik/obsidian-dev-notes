#react 

---

# Installing React Router

In this section, we are going to install React Router with the corresponding TypeScript types by carrying out the following steps:
1. Make sure the frontend project is open in Visual Studio Code and enter the following command to install React Router in the terminal:
`npm install react-router-dom`

> ![[zICO - Exclamation - 16.png]] Make sure **react-router-dom** version **6+** has been installed and listed in *package.json*. If version **5** has been installed, then version **6** can be installed by running `npm install react-router-dom@next`.

2. React router has a *peer dependency* on the **history** package, so let's install this using the terminal as well:
`npm install history`

> A **peer dependency** is a dependency that is not automatically installed by npm. This is why we have installed it in our project.

# Declaring routes

We declare the pages in our app using `BrowserRouter`, `Routes`, and `Route` components. 
- **BrowserRouter** is the top-level component that performs navigation between pages. 
- Paths to pages are defined in **Route** components nested within a `Routes` component. 
- The **Routes** component decides which `Route` component should be rendered for the current browser location.

We are going to start this section by creating blank pages that we'll eventually implement throughout this book. Then, we'll declare these pages in our app using `BrowserRouter`, `Routes`, and `Route` components.

## Creating some blank pages

Let's create blank pages for signing in, asking a question, viewing search results, and viewing a question with its answers by carrying out the following steps:
1. Create a file called *SignInPage.tsx* with the following content:
```tsx
import React from 'react';
import { Page } from './Page';

export const SignInPage = () => (
	<Page title="Sign In">{null}</Page>
);
```

Here, we have used the `Page` component we created in the previous chapter to create an empty page that has the title *Sign In*. We are going to use a similar approach for the other pages we need to create. Notice that we are rendering `null` in the content of the `Page` component at the moment. **This is a way of telling React to render nothing**.
2. Create a file called *AskPage.tsx* with the following content:
```jsx
import React from 'react';
import { Page } from './Page';

export const AskPage = () => (
	<Page title="Ask a question">{null}</Page>
);
```
3. Create a file called *SearchPage.tsx* with the following content:
```tsx
import React from 'react';
import { Page } from './Page';

export const SearchPage = () => (
	<Page title="Search Results">{null}</Page>
);
```
4. Create a file called *QuestionPage.tsx* with the following content:
```tsx
import React from 'react';
import { Page } from './Page';

export const QuestionPage = () => (
	<Page>Question Page</Page>
);
```

The title on the question page is going to be styled differently, which is why we are not using the `title` prop on the `Page` component. We have simply added some text on the page for the time being so that we can distinguish this page from the other pages.

# Creating a component containing routes

We are going to define all of the routes to the pages we created by carrying out the following steps:
1. Open *App.tsx* and add the following `import` statements under the existing import statements:
```tsx
import { BrowserRouter, Routes, Route } from 'reactrouter-dom';
import { AskPage } from './AskPage';
import { SearchPage } from './SearchPage';
import { SignInPage } from './SignInPage';
```
2. In the `App` component's JSX, add `BrowserRouter` as the outermost element:
```tsx
<BrowserRouter>
	<div css={ ... } >
		<Header />
		<HomePage />
	</div>
</BrowserRouter>
```
3. Let's define the routes in our app under the `Header` component, replacing the previous reference to `HomePage`:
```tsx
<BrowserRouter>
	<div css={ ... } >
		<Header />
		<Routes>
			<Route path="" element={<HomePage/>} />
			<Route path="search" element={<SearchPage/>} />
			<Route path="ask" element={<AskPage/>} />
			<Route path="signin" element={<SignInPage/>} />
		</Routes>
	</div>
</BrowserRouter>
```

> Each **route** is defined in a `Route` component that **defines what should be rendered** in an element prop for a given path in a `path` prop.  The route with the path that best matches the browser's location is rendered. 
> For example, if the browser location is http://localhost:3000/search, then the second `Route` component (that has path set to "*search*") will be the best match. This will mean that the `SearchPage` component is rendered.

> ![[zICO - Warning - 16.png]] We don't need a preceding slash (`/`) on the path because **React Router** performs relative matching by default.

4. Run the app by entering the `npm start` command in the Visual Studio Code terminal. We'll see that the home page renders just like it did before, which is great.
5. Now, enter */search* at the end of the browser location path. Here, we can see that React Router has decided that the best match is the `Route` component with a path of "*search*", and so renders the `SearchPage` component.

So, that's our basic routing configured nicely. What happens if the user enters a path in the browser that doesn't exist in our app? We'll find out in the next section.

## Handling routes not found

In this section, we'll handle paths that aren't handled by any of the `Route` components. By following these steps, we'll start by understanding what happens if we put an unhandled path in the browser:

Enter a path that isn't handled in the browser and see what happens. So, nothing is rendered beneath the header when we browse to a path that isn't handled by a `Route` component. This makes sense if we think about it.

We'd like to improve the user experience of routes not found and inform the user that this is the case. Let's add the following highlighted route inside the `Routes` component:
```tsx
<Routes>
	<Route path="" element={<HomePage/>} />
	<Route path="search" element={<SearchPage/>} />
	<Route path="ask" element={<AskPage/>} />
	<Route path="signin" element={<SignInPage/>} />
	<Route path="*" element={<NotFoundPage/>} />
</Routes>
```

> In order to understand how this works, let's think about what the `Routes` component does again – it renders the `Route` component that best matches the browser location. Path `*` will match any browser location, but isn't very specific. So, `*` won't be the best match for a browser location of `/`, `/search`, `/ask`, or `/signin`, but will catch invalid routes.

3. `NotFoundPage` hasn't been implemented yet, so let's create a file called *NotFoundPage.tsx* with the following content:
```tsx
import React from 'react';
import { Page } from './Page';

export const NotFoundPage = () => (
	<Page title="Page Not Found">{null}</Page>
);
```
4. Back in *App.tsx*, let's import the `NotFoundPage` component:
```tsx
import { NotFoundPage } from './NotFoundPage';
```
5. Now, if we enter an `/invalid` path in the browser, we'll see that our `NotFoundPage` component has been rendered.

> So, once we understand how the `Routes` component works, implementing a not-found page is very easy. We simply use a `Route` component with a path of `*` inside the `Routes` component.

At the moment, we are navigating to different pages in our app by manually changing the location in the browser. In the next section, we'll learn how to implement links to perform navigation within the app itself.

# Implementing links

In this section, we are going to use the `Link` component from *React Router* to declaratively perform navigation when clicking the app name in the app header. Then, we'll move on to programmatically performing navigation when clicking the *Ask a question* button to go to the ask page.

## Using the Link component

At the moment, when we click on *Q and A* in the top-left corner of the app, it is doing an HTTP request that returns the whole React app, which, in turn, renders the home page.

We are going to change this by making use of React Router's `Link` component so that navigation happens in the browser without an HTTP request. We are also going to make use of the `Link` component for the link to the sign-in page as well. We'll learn how to achieve this by performing the following steps:
1. In *Header.tsx*, import the `Link` component from React Router. Place the following line under the existing import statements:
```tsx
import { Link } from 'react-router-dom';
```
2. Let's change the `anchor` tag around the *Q & A* text to a Link element. The `href` attribute also needs to change to a `to` attribute:
```tsx
<Link
	to="/"
	css={ ... }
>
	Q & A
</Link>
```
3. Let's also change the sign-in link to the following:
```tsx
<Link
	to="signin"
	css={ ... }
>
	<UserIcon />
	<span>Sign In</span>
</Link>
```
4. If we go to the running app and click the *Sign In* link, we'll see the sign-in page rendered. Now, click on *Q & A* in the app header. We will be taken back to the home page, just like we wanted.
5. Execute step 4 again, but this time with the browser developer tools open and look at the Network tab. We'll find that, when clicking on the Sign In and Q & A links, no network requests are made.

So, the Link component is a great way of declaratively providing client-side navigation options in JSX. The task we performed in the last step confirms that all the navigation happens in the browser without any server requests, which is great for performance.

## Navigating programmatically

Sometimes, it is necessary to do navigation programmatically. Follow these steps to programmatically navigate to the ask page when the *Ask a question* button is clicked:
1. Import `useNavigate` from React Router into *HomePage.tsx*:
```tsx
import { useNavigate } from 'react-router-dom';
```
This is a **hook** that returns a function that we can use to perform a navigation.
2. Assign the `useNavigate` hook to a function called `navigate` just before the `handleAskQuestionClick` event handler:
```tsx
const navigate = useNavigate();
const handleAskQuestionClick = () => {
...
};
```
3. In `handleAskQuestionClick`, we can replace the `console.log` statement with the `navigation`:
```tsx
const handleAskQuestionClick = () => {
	navigate('ask');
};
```
4. In the running app, if we give this a try and click the *Ask a question* button, it will successfully navigate to the ask page.

So, we can declaratively navigate by using the `Link` component and programmatically navigate using the `useNavigate` hook in React Router. We will continue to make use of
the Link component in the next section.

# Using route parameters

In this section, we are going to define a `Route` component for navigating to the question page. This will contain a variable called `questionId` at the end of the path, so we will need to use what is called a **route parameter**. We'll implement more of the question page content in this section as well.

## Adding the question page route

Let's carry out the following steps to add the question page route:
1. In *App.tsx*, import the `QuestionPage` component we created earlier in this chapter:
```tsx
import { QuestionPage } from './QuestionPage';
```
2. In the `App` component's JSX, add a `Route` component for navigation to the question page inside the `Routes` component just above the wildcard route:
```tsx
<Routes>
	…
	<Route path="questions/:questionId" element={<QuestionPage />} />
	<Route path="*" element={<NotFoundPage/>} />
</Routes>
```

Note that the path we entered contains `:questionId` at the end.

> **Route parameters** are defined in the path with a colon in front of them. The value of the parameter is then available to destructure in the `useParams` hook.

> The `Route` component could be placed in any position within the `Routes` component. It is arguably more readable to keep the wildcard route at the bottom because this is the least specific path and therefore will be the last to be matched.

3. Let's go to *QuestionPage.tsx* and import `useParams` from React Router: 
```tsx
import { useParams } from 'react-router-dom';
```
5. We can destructure the value of the `questionId` route parameter from the `useParams` hook:
```tsx
export const QuestionPage = () => {
	const { questionId } = useParams();
	return <Page>Question Page</Page>;
};
```

We have also changed `QuestionPage` to have an explicit return statement.

5. For now, we are going to output `questionId` on the page as follows in the JSX:
```jsx
<Page>Question Page {questionId}</Page>;
```

We'll come back and fully implement the question page in Chapter 6, Working with Forms. For now, we are going to link to this page from the `Question` component.

6. So, in *Question.tsx*, add the following import statement to import the `Link` component:
```tsx
import { Link } from 'react-router-dom';
```
7. Now, we can wrap a `Link` component around the title text in the `Question` JSX while specifying the path to navigate to:
```tsx
<div
	css={css`
		padding: 10px 0px;
		font-size: 19px;
	`}
>
	<Link
		css={css`
			text-decoration: none;
			color: ${gray2};
		`}
		to={`/questions/${data.questionId}`}
>	
		{data.title}
	</Link>
</div>
```
8. Go to the running app and try clicking on the *Which state management tool should I use?* question. It will successfully navigate to the question page, showing the correct `questionId`.

So, we implement route parameters by defining variables in the route path with a colon in front and then picking the value up with the `useParams` hook.

# Implementing more of the question page

Let's carry out some more steps to implement the question page a little more:
1. In *QuestionsData.ts*, add a function that will simulate a web request to get a question:
```tsx
export const getQuestion = async (questionId: number): Promise<QuestionData | null> => {
	await wait(500);
	const results = questions.filter(q => q.questionId === questionId);
	return results.length === 0 ? null : results[0];
};
```

We have used the array `filter` method to get the question for the passed-in questionId.
Notice the type annotation for the function's return type. The type passed into the `Promise` generic type is `Question | null`, which is called a **union type**.

> A **union type** is a mechanism for defining a type that contains values from multiple types. If we think of a type as a set of values, then the union of multiple types is the same as the union of the sets of values. 
> More information is available at https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html#uniontypes.

So, the function is expected to asynchronously return an object of the `QuestionData` or `null` type.

2. Moving on to *QuestionPage.tsx*, let's import the function we just created, along with the question interface:
```tsx
import { QuestionData, getQuestion } from './QuestionsData';
```
3. Add the emotion `import` statement and import a couple of gray colors from our standard colors. Add the following lines at the top of *QuestionPage.tsx*:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
import { gray3, gray6 } from './Styles';
```
4. In the `QuestionPage` component, create a state for the question:
```tsx
export const QuestionPage = () => {
	const [
		question,
		setQuestion,
	] = React.useState<QuestionData | null>(null);
	const { questionId } = useParams();
	return <Page>Question Page {questionId}</Page>;
};
```
We are going to store the question in the state when the component is initially rendered.

> We are using a union type for the `state` because the state will be `null` initially while the question is being fetched, and also `null` if the question isn't found.

5. We want to call the `getQuestion` function during the initial render, so let's call it inside a call to the `useEffect` hook:
```tsx
export const QuestionPage = () => {
	…
	React.useEffect(() => {
	    const doGetQuestion = async (questionId: number) => {
	      const foundQuestion = await getQuestion(questionId);
	      setQuestion(foundQuestion);
	    };
	    if (questionId) {
	      doGetQuestion(Number(questionId));
	    }
	}, [questionId]);
return ...
};
```

So, when it is first rendered, the question component will fetch the question and set it in the state that will cause a second render of the component. 

> Note that we use the `Number` constructor to convert `questionId` from a string into a number. 

> ![[zICO - Exclamation - 16.png]] Also, note that the second parameter in the `useEffect` function has `questionId` in an array. This is because the function that `useEffect` runs (the first parameter) is dependent on the 
> `questionId` value and should rerun if this value changes. If `[questionId]` wasn't provided, it would get into an infinite loop because every time it called `setQuestion`, it causes a re-render, which, without `[questionId]`, would always rerun the method.

6. Let's start to implement the JSX for the `QuestionPage` component by adding a container element for the page and the question title:
```tsx
<Page>
	<div
		css={css`
			background-color: white;
			padding: 15px 20px 20px 20px;
			border-radius: 4px;
			border: 1px solid ${gray6};
			box-shadow: 0 3px 5px 0 rgba(0, 0, 0, 0.16);
		`}
	>
		<div
			css={css`
				font-size: 19px;
				font-weight: bold;
				margin: 10px 0px 5px;
			`}
		>	
			{question === null ? '' : question.title}
		</div>
	</div>
</Page>
```

We don't render the title until the question state has been set. The question state is `null` while the question is being fetched, and it remains `null` if the question isn't found. 

Note that we use a triple equals (`===`) to check whether the question variable is `null` rather than a double equals (`==`).

> When using triple equals (`===`), we are checking for **strict equality**. This means both the type and the value we are comparing have to be the same. 
> When using a double equals (`==`), the type isn't checked. 
> Generally, it is good practice to use the triple equals (`===`) to perform a strict equality check.

If we look at the running app, we will see that the question title has been rendered in a nice white card.

7. Let's now implement the question content:
```tsx
<Page>
	<div ... >
		<div ... >
			{question === null ? '' : question.title}
		</div>
		{question !== null && (
			<React.Fragment>
				<p 
					css={css`
						margin-top: 0px;
						background-color: white;
					`}
				>
					{question.content}
				</p>
			</React.Fragment>
		)}
	</div>
</Page>
```

So, we show the output content from the question in JSX if the question state has been set from the fetched data. Note that this is nested within a `Fragment` component—what is this for?

> In React, **a component can only return a single element**. This rule applies to conditional rendering logic where there can be only a single parent React element being rendered. React `Fragment` allows us to work around this rule because we can nest multiple elements within it without creating a DOM node.

8. Let's add when the question was asked and who asked it into the `Fragment`:
```tsx
{question !== null && (
	<React.Fragment>
		<p ... >
			{question.content}
		</p>
		<div
			css={css`
				font-size: 12px;
				font-style: italic;
				color: ${gray3};
			`}
		>
			{`Asked by ${question.userName} on ${question.created.toLocaleDateString()} ${question.created.toLocaleTimeString()}`}
		</div>
	</React.Fragment>
)}
```

So, the question page is looking nice now. We aren't rendering any answers yet though, so let's look at that next.

# Creating an AnswerList component

Follow these steps to create a component that will render a list of answers:
1. Create a new file called *AnswerList.tsx* with the following import statements:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
import React from 'react';
import { AnswerData } from './QuestionsData';
import { Answer } from './Answer';
import { gray5 } from './Styles';
```
So, we are going to use an unordered list to render the answers without the bullet points. We have referenced a component, `Answer`, that we'll create later in these steps.

2. Let's define the interface so that it contains a data prop for the array of answers:
```tsx
interface Props {
	data: AnswerData[];
}
```
3. Let's create the `AnswerList` component, which outputs the answers:
```tsx
export const AnswerList = ({ data }: Props) => (
	<ul
		css={css`
			list-style: none;
			margin: 10px 0 0 0;
			padding: 0;
		`}
	>
		{data.map(answer => (
			<li
				css={css`
					border-top: 1px solid ${gray5};
				`}
				key={answer.answerId}
			>
				<Answer data={answer} />
			</li>
		))}
	</ul>
);
```

Each answer is output in an unordered list in an `Answer` component, which we'll implement next.

4. Let's move on and implement the `Answer` component by creating a file called *Answer.tsx* with the following import statements:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
import React from 'react';
import { AnswerData } from './QuestionsData';
import { gray3 } from './Styles';
```
5. The interface for the Answer component is simply going to contain the answer data:
```tsx
interface Props {
	data: AnswerData;
}
```
6. Now, the `Answer` component will simply render the answer content, along with who answered it and when it was answered:
```tsx
export const Answer = ({ data }: Props) => (
	<div
		css={css`
			padding: 10px 0px;
		`}
	>
		<div
			css={css`
				padding: 10px 0px;
				font-size: 13px;
			`}
		>
			{data.content}
		</div>
		<div
			css={css`
				font-size: 12px;
				font-style: italic;
				color: ${gray3};
			`}
		>
			{`Answered by ${data.userName} on ${data.created.toLocaleDateString()} ${data.created.toLocaleTimeString()}`}
		</div>
	</div>
);
```
7. Let's now go back to *QuestionPage.tsx* and import `AnswerList`:
```tsx
import { AnswerList } from './AnswerList';
```
8. Now, we can add `AnswerList` to the `Fragment` element:
```tsx
{question !== null && (
	<React.Fragment>
		<p ... >
			{question.content}
		</p>
		<div ... >
			{`Asked by ${question.userName} on ${question.created.toLocaleDateString()} ${question.created.toLocaleTimeString()}`}
		</div>
		<AnswerList data={question.answers} />
	</React.Fragment>
)}
```

If we look at the running app on the question page at `questions/1`, we'll see the answers rendered nicely.
That completes the work we need to do on the question page in this chapter. However, we need to allow users to submit answers to a question, which we'll cover in Chapter 6, Working with Forms.

# Using query parameters

A query parameter is part of the URL that allows additional parameters to be passed into a path. For example, `/search?criteria=typescript` has a query parameter called `criteria` with a value of `typescript`. Query parameters are sometimes referred to as a **search parameter**.

In this section, we are going to implement a query parameter on the search page called `criteria`, which will drive the search. We'll implement the search page along the way.
Let's carry out the following steps to do this:
1. We are going to start in *QuestionsData.ts* by creating a function to simulate a search via a web request:
```tsx
export const searchQuestions = async (
	criteria: string): Promise<QuestionData[]> => {
		await wait(500);
		return questions.filter(q => q.title.toLowerCase().indexOf(criteria.toLowerCase()) >= 0 || q.content.toLowerCase().indexOf(criteria.toLowerCase()) >= 0
	);
};
```

So, the function uses the array `filter` method and matches the criteria to any part of the question title or content.

2. Let's import this function, along with the other items we need, into `SearchPage.tsx`. Place these statements above the existing import statements in *SearchPage.tsx*:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react'
import { useSearchParams } from 'react-router-dom';
import { QuestionList } from './QuestionList';
import { searchQuestions, QuestionData } from './QuestionsData';
```
The `useSearchParams` hook from React Router is used to access query parameters.

3. Add an explicit `return` statement to the `SearchPage` component and destructure the object from `useSearchParams` that contains the search parameters:
```tsx
export const SearchPage = () => {
	const [searchParams] = useSearchParams();
	return (
		<Page title="Search Results">{null}</Page>
	);
};
```

> The `useSearchParams` hook returns an array with two elements. 
>	- The first element is an object containing the search parameters, 
>	- The second element is a function to update the query parameters. 

We have only destructured the first element in our code because we don't need to update query parameters in this component.

4. We are now going to create some state to hold the matched questions in the search:
```tsx
export const SearchPage = () => {
	const [searchParams] = useSearchParams();
	const [
		questions,
		setQuestions,
	] = React.useState<QuestionData[]>([]);
	return …
};
```

5. Next, we are going to get the `criteria` query parameter value:
```tsx
export const SearchPage = () => {
	const [searchParams] = useSearchParams();
	const [
		questions,
		setQuestions,
	] = React.useState<QuestionData[]>([]);
	const search = searchParams.get('criteria') || "";
	return …
};
```

> The `searchParams` object contains a `get` method that can be used to get the value of a query parameter.

6. We are going to invoke the search when the component first renders and when the search variable changes using the `useEffect` hook:
```tsx
const search = searchParams.get('criteria') || '';
React.useEffect(() => {
	const doSearch = async (criteria: string) => {
	const foundResults = await searchQuestions(criteria);
	setQuestions(foundResults);
	};
	doSearch(search);
}, [search]);
```

7. We can now render the search criteria under the page title. Replace `{null}` with the highlighted code:
```tsx
<Page title="Search Results">
	{search && (
		<p
			css={css`
				font-size: 16px;
				font-style: italic;
				margin-top: 0px;
			`}
		>
			for "{search}"
		</p>
	)}
</Page>
```
8. The last task is to use the `QuestionList` component to render the questions that are returned from the search:
```tsx
<Page title="Search Results">
	{search && (
		<p ... >
			for "{search}"
		</p>
	)}
	<QuestionList data={questions} />
</Page>
```

Our `QuestionList` component is now used in both the *home* and *search* pages with different data sources. The reusability of this component has been made possible because we have followed the *container pattern* we briefly mentioned in Chapter 3, Getting Started with React and TypeScript.

In the running app, enter `/search?criteria=type` in the browser. The search will be invoked and the results will be rendered as we would expect.

So, the `useSearchParams` hook in React Router makes interacting with query parameters nice and easy. In Chapter 6, Working with Forms, we'll wire up the search box in the header to our search form.

# Lazy loading routes

At the moment, all the JavaScript for our app is loaded when the app first loads. This is fine for small apps, but for large apps, this can have a negative impact on performance. There may be large pages that are rarely used in the app that we want to load the JavaScript for on demand. This process is called **lazy loading**.

We are going to lazy load the *ask page* in this section. It isn't a great use of lazy loading because this is likely to be a popular page in our app, but it will help us learn how to implement this. Let's carry out the following steps:
1. First, we are going to add a default export to the `AskPage` component in *AskPage.tsx*:
```tsx
const AskPage = () => <Page title="Ask a question">{null}</Page>;
export default AskPage;
```
2. Open *App.tsx* and remove the current import statement for the `AskPage` component.
3. Add an import statement for React:
```tsx
import React from 'react';
```
4. Add a new import statement for the `AskPage` component **after all the other import statements**:
```tsx
const AskPage = React.lazy(() => import('./AskPage'));
```

> It is important that this is the last import statement in the file because, otherwise, ESLint may complain that the import statements beneath it are in the body of the module.

> The `lazy` function in React lets us render a **dynamic import** as a regular component. 
> A **dynamic import** returns a promise for the requested module that is resolved after it has been fetched, instantiated, and evaluated.

5. So, the `AskPage` component is being loaded on demand now, but the `App` component is expecting this component to be loaded immediately. If we enter the ask path in the browser's address bar and press the Enter key, we may receive an error with a clue of how to resolve this.

6. As suggested by the error message, we are going to use the `Suspense` component from React to resolve this issue. For ask `Route`, we wrap the `Suspens`e component around the `AskPage` component:
```tsx
<Route
	path="ask"
	element={
		<React.Suspense
			fallback={
				<div
					css={css`
						margin-top: 100px;
						text-align: center;
					`}
				>
					Loading...
				</div>
			}
		>
			<AskPage />
		</React.Suspense>
	}
/>
```

The `Suspense` fallback prop allows us to render a component while `AskPage` is loading. So, we are rendering a *Loading...* message while the `AskPage` component is being loaded.

7. Let's go to the running app on the home page and open the browser developer tools by pressing F12.
8. On the Network tab, let's clear the previous network activity by clicking the no entry icon. Then, if we click the *Ask a question* button, we will see confirmation that additional JavaScript has been downloaded in order to render the `AskPage` component.
9. The `AskPage` component loads so fast that we are unlikely to see the `Loading` component being rendered. In the Chrome browser developer tools, there is an option to simulate a Slow 3G network in the Network tab.
10. If we turn this on, load the app again by pressing F5 on the home page, and click the *Ask a question* button, we will see the *Loading...* message being rendered temporarily.

In this example, the `AskPage` component is small in size, so this approach doesn't really positively impact performance. However, loading larger components on demand can really help performance, particularly on slow connections.
