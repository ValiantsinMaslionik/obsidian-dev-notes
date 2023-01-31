#react 

---

Components can have properties that allow consumers to pass parameters into them, just like when we pass parameters into a JavaScript function. React function components accept a single parameter named `props`, which holds its properties. The word "props" is short for "properties".
In this section, we'll learn all about how to implement strongly typed props, including optional and default props. Then, we'll implement the rest of the home page to assist in our learning.

# Creating HomePage child components

We are going to implement some child components that the `HomePage` component will use. We will pass the unanswered questions data to the child components via `props`. 

# Creating the `QuestionList` component

Let's go through the following steps to implement the `QuestionList` component:
1. Let's create a file called *QuestionList.tsx* in the *src* folder and add the following import statements:
```tsx
import React from 'react';
import { QuestionData } from './QuestionsData';
```
2. Now, let's define the interface for the component `props` underneath the `import` statements:
```tsx
interface Props {
  data: QuestionData[];
}
```

3. Let's start by implementing the `QuestionList` component: 
```tsx
export const QuestionList = (props: Props) => <ul></ul>;
```

4. Now, we can inject the data into the list:
```tsx
export const QuestionList = (props: Props) => (
	<ul>
		{props.data.map((question) => (
			<li key={question.questionId}></li>
		))}
	</ul>
);
```

We are using the `map` method within the data array to iterate through the data that's been passed into the component.

> `map` is a **standard method that is available in a JavaScript array**. The method iterates through the items in the array, invoking the function that's passed into it for each array item. The function is expected to return an item that will form a new array. In summary, it is a way of mapping an array to a new array. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/GlobalObjects/Array/map.

So, we iterate through the questions that are passed into QuestionList and render a li HTML element for each array item.
Notice the key prop we pass into the li element.

> ![[zICO - Warning - 16.png]] The `key` prop helps React detect when the element changes or is added or removed. When we output content in a loop, in React, **it is good practice to apply this prop and set it to a unique value within the loop**. This helps React distinguish it from the other elements during the rendering process. If we don't provide a key prop, React will make unnecessary changes to the DOM that can impact performance. More information can be found at https://reactjs.org/docs/lists-and-keys.html.

5. Our `QuestionList` component will work perfectly fine, but we are going to make one small change that will make the implementation a little more succinct. Here, we are going to destructure the props into a data variable in the function parameter:
```jsx
export const QuestionList = ({ data }: Props) => (
	<ul>
		{data.map((question) => (
			<li key={question.questionId}></li>
		))}
	</ul>
);
```

> **Destructuring** is a special syntax that allows us to **unpack objects or arrays into variables**. More information on destructuring can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment.

Notice that we directly reference the `data` variable in the JSX and not through the `props` variable, like we did in the previous example. This is a nice pattern to use, particularly when there are more props.

Before we can complete the QuestionList component, we must create its child component, `Question`, which we'll do next.

# Creating the Question component

Follow these steps to implement the `Question` component: 
1. Create a file called *Question.tsx* in the *src* folder, which contains the following import statements:
```tsx
import React from 'react';
import { QuestionData } from './QuestionsData';
```
2. Let's create the props type for the `Question` component, which will simply contain a prop for the question data:
```tsx
interface Props {
	data: QuestionData;
}
```
3. Now, we can create the component:
```tsx
export const Question = ({ data }: Props) => (
<div>
	<div>
		{data.title}
	</div>
	<div>
		{`Asked by ${data.userName} on ${data.created.toLocaleDateString()} ${data.created.toLocaleTimeString()}`}
	</div>
</div>
);
```

So, we are rendering the question title, who asked the question, and when it was asked.
Notice that we are using the `toLocaleDateString` and `toLocaleTimeString` functions on the `data.createdDate` object to output when the question was asked.

> Dates are often displayed in different formats in different countries. For example, February 1, 2021 can be displayed as 02/01/21 or 01/02/21 in different countries. `toLocaleDateString` and `toLocaleTimeString` are methods on the `Date` object that format the date and time according to the browser's locale. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleDateStringand https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleTimeString.

# Wiring up the components

Now, we can wire up the components we have just created using our props so that we get the unanswered questions rendered on the home page. Follow these steps to do so:
1. Let's go back to *QuestionList.tsx* and import the `Question` component we just created:
```tsx
import { Question } from './Question';
```
2. Now, we can place an instance of the `Question` component inside the `QuestionList` JSX that's nested within the `li` element:
```jsx
{data.map((question) => (
	<li key={question.questionId}>
		<Question data={question} />
	</li>
))}
```
3. Moving on to the `HomePage` component in *HomePage.tsx*, let's import the `QuestionList` component. Let's also import the `getUnansweredQuestions` function we created earlier, which returns unanswered questions:
```tsx
import { QuestionList } from './QuestionList';
import { getUnansweredQuestions } from './QuestionsData';
```
4. Now, we can place an instance of `QuestionList` inside the `HomePage` component JSX, inside the outermost `div` tag:
```tsx
<div>
	<div>
		<h2>Unanswered Questions</h2>
		<button>Ask a question</button>
	</div>
	<QuestionList data={getUnansweredQuestions()} />
</div>
```

Notice that we pass the array of questions into the data prop by calling the `getUnansweredQuestions` function we created and imported earlier in this chapter.
If we had more than one unanswered question in our mock data, they would be the output on our home page.

We are going to finish this section on props by understanding **optional** and **default** props, which can make our components more flexible for consumers.

# Optional and default props

A prop can be **optional** so that the consumer doesn't necessarily have to pass it into a component. For example, we could have an optional prop in the `Question` component that allows a consumer to change whether the content of the question is rendered or not.

We'll do this now:
1. We need to add the content to the `Question` component, so add the following code beneath the question title in the JSX:
```tsx
export const Question = ({ data }: Props) => (
	<div>
		<div>
			{data.title}
		</div>
		<div>
			{data.content.length > 50
				? `${data.content.substring(0, 50)}...`
				: data.content}
		</div>
		<div>
			{`Asked by ${data.userName} on ${data.created.toLocaleDateString()} ${data.created.toLocaleTimeString()}`}
		</div>
	</div>
);
```

Here, we have used a **JavaScript ternary operator** to truncate the content if it is longer than 50 characters.

> A JavaScript **ternary** is a short way of implementing a conditional statement that results in one of two branches of logic being executed. The statement contains three operands separated by a question mark `?` and a colon `:`. The first operand is a condition, the second is what is returned if the condition is true, and the third is what is returned if the condition is false. The ternary operator is a popular way of implementing conditional logic in JSX. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator.

We have also used **template literals** and **interopolation** in this code snippet.

> **JavaScript template literals** are strings contained in backticks \`. A template literal can include expressions that inject data into the string. Expressions are contained in curly brackets after a dollar sign. This is often referred to as interpolation. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals.

2. Create an additional property in the `Props` interface in *Question.tsx* to represent whether the question's content is shown:
```tsx
interface Props {
	data: QuestionData;
	showContent: boolean;
}
```
3. Let's destructure the `showContent` prop in the `Question` component parameter:
```tsx
export const Question = ({ data, showContent }: Props) =>
```
4. Let's change where we render the question content to the following:
```tsx
<div>
	{data.title}
</div>
{showContent && (
	<div>
		{data.content.length > 50
			? `${data.content.substring(0, 50)}...`
			: data.content}
	</div>
)}
<div>
	{`Asked by ${data.userName} on ${data.created.toLocaleDateString()} ${data.created. toLocaleTimeString()}`}
</div>
```

We have just changed the component so that it only renders the question's content if the `showContent` prop is `true` using the short-circuit operator, `&&`.

> The **short-circuit operator** `&&` is another way of expressing conditional logic. It has two operands, with the first being the condition and the second being the logic to execute if the condition evaluates to `true`. It is often used in JSX to conditionally render an element if the condition is true.

5. If we go back to *QuestionList.tsx*, we'll see a TypeScript compilation error where the `Question` component is referenced.
This is because `showContent` is a required prop in the `Question` component and we haven't passed it in. It can be a pain to always have to update consuming components when a prop is added. Couldn't `showContent` just default to `false` if we don't pass it in? Well, this is exactly what we are going to do next.
6. Move back into *Question.tsx* and make the `showContent` prop optional by adding a question mark after the name of the prop in the interface:
```tsx
interface Props {
data: QuestionData;
showContent?: boolean;
}
```

> **Optional properties** are actually a TypeScript feature. Function parameters can also be made **optional** by putting a question mark at the end of the parameter name before the type annotation; for example, `duration?:number`.

Now, the compilation error in *QuestionList.tsx* has gone away and the app will render the unanswered questions without their content.

What if we wanted to show the question's content by default and allow consumers to suppress this if required?
We'll do just this using two different approaches to default props.
7. We can set a special object literal called `defaultProps` on the component to define the default values:
```tsx
export const Question = ({ data, showContent }: Props) =>
(
...
);

Question.defaultProps = {
	showContent: true,
};
```

8. There is another way of setting default props that's arguably neater. Let's remove the `defaultProps` object literal and specify the default after the destructured component's `showContent` parameter:
```tsx
export const Question = ({ data, showContent = true }: Props) => ( ... )
```

This arguably makes the code more readable because the default is right next to its parameter. This means our eyes don't need to scan right down to the bottom of the function to see that there is a default value for a parameter.

So, our home page is looking good in terms of code structure. However, there are a couple of components in *HomePage.tsx* that can be extracted so that we can reuse them as we develop the rest of the app. We'll do this next.

# Children prop

The children prop is a magical prop that all React components automatically have. It can be used to render child elements. It's magical because it's automatically there, without us having to do anything, as well as being extremely powerful. In the following steps, we'll use the children prop when creating `Page` and `PageTitle` components:
1. First, let's create a file called *PageTitle.tsx* in the *src* folder with the following content:
```tsx
import React from 'react';

interface Props {
	children: React.ReactNode;
}

export const PageTitle = ({ children }: Props) => <h2>{children}</h2>;
```

We define the `children` prop with a type annotation of `ReactNode`. This will allow us to use a wide range of child elements, such as other React components and plain text.
We have referenced the children prop inside the `h2` element. This means that the child elements that consuming components specify will be placed inside the `h2` element.

2. Let's create a file called *Page.tsx* with the following content:
```tsx
import React from 'react';
import { PageTitle } from './PageTitle';

interface Props {
	title?: string;
	children: React.ReactNode;
}

export const Page = ({ title, children }: Props) => (
	<div>
		{title && <PageTitle>{title}</PageTitle>}
		{children}
	</div>
);
```

Here, the component takes in an optional `title` prop and renders this inside the `PageTitle` component. The component also takes in a `children` prop. In the consuming component, the content nested within the `Page` component will be rendered where we have just placed the children prop.
3. Let's move back to *HomePage.tsx* now and import the `Page` and `PageTitle` components:
```tsx
import { Page } from './Page';
import { PageTitle } from './PageTitle';
```
4. Let's use the `Page` and `PageTitle` components in the `HomePage` component, as follows:
```tsx
export const HomePage = () => (
	<Page>
		<div>
			<PageTitle>Unanswered Questions</PageTitle>
			<button>Ask a question</button>
		</div>
		<QuestionList data={getUnansweredQuestions()} />
	</Page>
);
```

Notice that we aren't taking advantage of the `title` prop in the `Page` component in `HomePage`. This is because this page needs to have the *Ask a question* button to the right of the title, so we are rendering this within `HomePage`. However, other pages that we implement will take advantage of the `title` prop we have implemented
.
So, the children prop allows a consumer to render custom content within the component. This gives the component flexibility and makes it highly reusable, as we'll discover when we use the `Page` component throughout our app. Something you may not know, however, is that the children prop is actually a function prop. We'll learn about function props in the next section.

# Function props

Props can consist of primitive types, such as the boolean `showContent` prop we implemented in the `Question` component. Props can also be objects and arrays, as we have seen with the `Question` and `QuestionList` components. This in itself is powerful. However, **props can also be functions**, which allows us to implement components that are extremely flexible.

Using the following steps, we are going to implement a function prop on the `QuestionList` component that allows the consumer to render the question as an alternative to `QuestionList` rendering it:
1. In *QuestionList.tsx*, add a `renderItem` function prop to the `Props` interface, as follows:
```tsx
interface Props {
	data: QuestionData[];
	renderItem?: (item: QuestionData) => JSX.Element;
}
```
2. So, the `renderItem` prop is a function that takes in a parameter containing the question and returns a JSX element. Notice that we have made this an optional prop so that our app will continue to run just as it was previously.
3. Let's destructure the function parameters into a renderItem variable:
```jsx
export const QuestionList = ({ data, renderItem }: Props) => 
…
```
4. Now, we can call the `renderItem` function prop in the JSX if it has been passed and, if not, render the `Question` component:
```jsx
{data.map((question) => (
	<li key={question.questionId}>
		{renderItem ? renderItem(question) : <Question data={question} />}
	</li>
))}
```
5. Notice that we are using `renderItem` in the ternary condition, even though it isn't a boolean.

> Conditions in `if` statements and *ternaries* will execute the second operand if the condition evaluates to truthy, and the third operand if the condition evaluates to falsy. **`true` is only one of many truthy values**. 
> ![[zICO - Warning - 16.png]] In fact, `false`, `0`, `""`, `null`, `undefined`, and `NaN` are falsy values and everything else is truthy.
 
So, `renderItem` will be truthy and will execute if it has been passed as a prop.

6. Our app will render the unanswered questions, just like it did previously, by rendering the `Question` component. Let's try our `renderItem` prop out by opening *HomePage.tsx* and setting this to the following in the `QuestionList` element:
```tsx
<QuestionList
	data={getUnansweredQuestions()}
	renderItem={(question) => <div>{question.title}</div>}
/>
```

> The *pattern of implementing a function prop to allow consumers to render an internal piece of the component* is often referred to as a render prop. It makes the component extremely flexible and useable in many different scenarios.

7. Now that we understand what a render prop is, we are going to revert this change and have `QuestionList` take back control of rendering the questions:
```jsx
<QuestionList
	data={getUnansweredQuestions()}
	/*renderItem={(question) => <div>{question.title}</div>}*/
/>
```
