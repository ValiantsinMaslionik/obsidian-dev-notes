#react

---

# Understanding controlled components

In this section, we are going to learn how to use what are called **controlled components** to implement a form. 

> A **controlled component** has its value synchronized with the state in React. 
 
This will make more sense when we've implemented our first controlled component. Let's open our project in Visual Studio Code and change the search box in our app header to a controlled component. Follow these steps:
1. Open *Header.tsx* and add the following imports:
```tsx
import {
	Link,
	useSearchParams,
} from 'react-router-dom';
```

2. The default value for the search box is going to be the `criteria` route query parameter. So, let's use the `useSearchParams` hook from React Router to get this:
```tsx
export const Header = () => {
	// [] - object/array destructuring
	const [searchParams] = useSearchParams();
	const criteria = searchParams.get('criteria') || '';
	const handleSearchInputChange = ...
}
```

3. Let's create a state that we can store the search value in, defaulting it to the criteria variable we have just set:
```tsx
const [searchParams] = useSearchParams();
//const searchParams = new URLSearchParams(location.search);
const criteria = searchParams.get('criteria') || '';
const [search, setSearch] = React.useState(criteria);
const handleSearchInputChange = ...
```

4. Now, let's drive the search box value from this search state:
```tsx
<input
	type="text"
	placeholder="Search..."
	value={search}
	onChange={handleSearchChange}
	css={ ... }
/>
```

5. Start the app by running the `npm start` command in Visual Studio Code's Terminal.

Try to type something into the search box in the app header. You'll notice that nothing seems to happen; something is preventing us from entering the value. 
**We have just set the value to some React state, so React is now controlling the value of the search box**. This is why we no longer appear to be able to type into it.

We are part way through creating our first controlled input. However, controlled inputs aren't much use if users can't enter anything into them. So, how can we make our input editable again? The answer is that we need to listen to changes that have been made to the input value and update the state accordingly. React will then render the new value from the state.

1. We are already listening to changes with the handleSearchInputChange function. So, all we need to do is update the state in the following function, replacing the previous console.log statement with the following:
```tsx
const handleSearchChange = (e: ChangeEvent<HTMLInputElement>) => {
	setSearch(e.currentTarget.value);
};
```
Now, if we go to the running app and enter something into the search box, this time, it will behave as expected, allowing us to enter characters into it.

2. Now, add a form element that's wrapped around the input element:
```tsx
<form>
	<input
		type="text"
		placeholder="Search..."
		onChange={handleSearchInputChange}
		value={search}
		css={ ... }
	/>
</form>
```
**Eventually, this will allow a user to invoke the search when the Enter key is pressed.**

3. Add the following `onSubmit` prop to the form element:
```jsx
<form onSubmit={handleSubmit}>
```
4. Add the implementation of the submit handler just above the `return` statement:
```tsx
const handleSubmit = (e: React.FormEvent) => {
	e.preventDefault();
	console.log(search);
};
return …
```
5. In the running app, type something into the search box in the app header and press *Enter*. Open the browser's DevTools and look at the console.
Now, if we type characters into the search box and press *Enter*, we will see the submitted search criteria in the console.
6. Press Ctrl + C in the Terminal and press Y when prompted to stop the app from running.

In summary, controlled components have their values managed by React's state. It is important that we implement a change handler that updates the state; otherwise, our users won't be able to interact with the component.

If we were to implement a form with several controlled components, we would have to create the state and a change event listener to update the state for each field. That's quite a lot of boilerplate code to write when implementing forms. Is there a forms library that we can use to reduce the amount of repetitive code we must write? Yes! We'll do just this in the next section.

# Reducing boilerplate code with React

## Hook Form

In this section, we are going to use a popular forms library called **React Hook Form**. This library reduces the amount of code we need to write when implementing forms. Once we have installed React Hook Form, we will refactor the search form we created in the previous section. We will then use React Hook Form to implement forms for asking a question and answering a question.

### Installing React Hook Form

Let's install React Hook Form by entering the following command into the Terminal:
```
npm install react-hook-form
```

The *react-hook-form* package includes TypeScript types, so these aren't in a separate package that we need to install.

## Refactoring the Header component to use React Hook Form

We are going to use React Hook Form to reduce the amount of code in the `Header` component. Open *Header.tsx* and follow these steps:
1. Remove the line where the search state is declared. The line you must remove is shown here:
```
const [search, setSearch] = React.useState(criteria);
```
React Hook Form will manage the field state, so we don't have to write explicit code for this.

2. Add the following name property to the input element:
```tsx
<input
	name="search"
	type="text"
	…
/>
```

> ![[zICO - Warning - 16.png]] The `name` property is required by React Hook Form and must have a unique value for a given form. We will eventually be able to access this value from the input element using this name.

3. Remove the value and `onChange` properties from the `input` element and add a `defaultValue` property, which is set to the criteria query parameter value:
```tsx
<input
	name="search"
	type="text"
	placeholder="Search..."
	defaultValue={criteria}
	css={ ... }
/>
```
React Form Hook will eventually manage the value of the input. Note that `defaultValue` is a property of the input element for setting its initial value.

4. Remove the `handleSearchInputChange` function handler for the input's  change event.
5. Remove the `console.log` statement from the `handleSubmit` function:
```tsx
const handleSubmit = (e: React.FormEvent) => {
	e.preventDefault();
};
```

6. Now that we have removed the boilerplate form code, it's time to use React Hook Form. 

Let's start by importing the `useForm` hook from React Hook Form:
```tsx
import { useForm } from 'react-hook-form';
```
7. Add a type that will represent the form data just above the `Header` component definition and below the import statements:
```tsx
type FormData = {
	search: string;
};
```

8. Pass the form data type into the `useForm` hook and destructure the register function from it:
```tsx
export const Header = () => {
	const { register } = useForm<FormData>();
	const [searchParams] = useSearchParams();
...
```

9. Add a `ref` property to the `input` element in the `Header` component's JSX and set this to the register function from React Hook Form:
```tsx
<input
	ref={register}
	name="search"
	type="text"
	placeholder="Search..."
	defaultValue={criteria}
	css={ ... }
/>
```
The register function allows an `input` element to be registered with React Hook Form and then be managed by it. It needs to be set to the `ref` property on the element.

> The `ref` property is a special property that React adds to elements that enables the underlying DOM node to be accessed.

The code for our form is a lot shorter now. This is because React Hook Form holds the field state and manages updates to it.
We used the `register` function from the `useForm` hook to tell React Hook Form which fields to manage. There are other useful functions and objects in the `useForm` hook that we will learn about and use in this chapter.

React Form Hook is now controlling the search input field. We will return to this and implement the search submission ability in the *Submitting forms* section.

# Creating form styled components

In this section, we are going to create some *styled* components that can be used in the forms that we will eventually implement. Open *Styles.ts* and follow these steps:
1. Import the css function from *emotion*:
```ts
import { css } from '@emotion/react';
```

2. Add a *styled* component for a `fieldset` element:
```ts
export const Fieldset = styled.fieldset`
	margin: 10px auto 0 auto;
	padding: 30px;
	width: 350px;
	background-color: ${gray6};
	border-radius: 4px;
	border: 1px solid ${gray5};
	box-shadow: 0 3px 5px 0 rgba(0, 0, 0, 0.16);
`;
```
We will eventually use a `fieldset` element inside our forms.

3. Add a *styled* component for the field container:
```tsx
export const FieldContainer = styled.div`
	margin-bottom: 10px;
`;
```
4. Add a *styled* component for the `label` element:
```ts
export const FieldLabel = styled.label`
	font-weight: bold;
`;
```
5. The field editor elements will have many common CSS properties. Create a variable that contains the following code:
```ts
const baseFieldCSS = css`
	box-sizing: border-box;
	font-family: ${fontFamily};
	font-size: ${fontSize};
	margin-bottom: 5px;
	padding: 8px 10px;
	border: 1px solid ${gray5};
	border-radius: 3px;
	color: ${gray2};
	background-color: white;
	width: 100%;
	:focus {
	outline-color: ${gray5};
	}
	:disabled {
	background-color: ${gray6};
	}
`;
```

6. Use the following variable in a *styled* component for an `input` element:
```ts
export const FieldInput = styled.input`
	${baseFieldCSS}
`;
```

This causes the `input` element to include the CSS from the `baseFieldCSS` variable in the new *styled* component we are creating.

7. Now, create a *styled* component for the `textarea` element:
```ts
export const FieldTextArea = styled.textarea`
	${baseFieldCSS}
	height: 100px;
`;
```

8. Add a *styled* component for the validation error message:
```ts
export const FieldError = styled.div`
	font-size: 12px;
	color: red;
`;
```
9. Add a *styled* component for a container for the form submit button:
```ts
export const FormButtonContainer = styled.div`
	margin: 30px 0px 0px 0px;
	padding: 20px 0px 0px 0px;
	border-top: 1px solid ${gray5};
`;
```

10. The last *styled* components we are going to create are the submission messages:
```ts
export const SubmissionSuccess = styled.div`
	margin-top: 10px;
	color: green;
`;

export const SubmissionFailure = styled.div`
	margin-top: 10px;
	color: red;
`;
```

With that, we've implemented all the *styled* components that we will use in our forms.
Now that we have implemented these *styled* components, we will use these to implement our next form.

# Implementing the ask form

Now, it's time to implement the form so that our users can ask a question. We'll do this by leveraging React Hook Form and our form's *styled* components. Follow these steps:
1. Open *AskPage.tsx* and add the following import statements:
```tsx
import {
	Fieldset,
	FieldContainer,
	FieldLabel,
	FieldInput,
	FieldTextArea,
	FormButtonContainer,
	PrimaryButton,
} from './Styles';
import { useForm } from 'react-hook-form';
```

2. Add a type to represent the form data just above the `AskPage` component definition and just below the `import` statements:
```tsx
type FormData = {
	title: string;
	content: string;
};
```

3. Add an explicit `return` statement to the `AskPage` component, pass in the `FormData` type to the `useForm` hook, and destructure the `register` function from it:
```tsx
export const AskPage = () => {
	const { register } = useForm<FormData>();
	return (
		<Page title="Ask a question">
			{null}
		</Page>
	);
}
```
5. Add the `form` and `Fieldset` elements, replacing the `null` output:
```tsx
<Page title="Ask a question">
	<form>
		<Fieldset>
		</Fieldset>
	</form>
</Page>
```

6. Now, let's add a field that will capture the question's title:
```tsx
<Fieldset>
	<FieldContainer>
		<FieldLabel htmlFor="title">
			Title
		</FieldLabel>
		<FieldInput
			id="title"
			type="text"
			{...register("title")}
		/>
	</FieldContainer>
</Fieldset>
```

> Notice how we have tied the label to the `input` using the `htmlFor` attribute. This means a screen reader will read out the label when the input has focus. 
> In addition, clicking on the label will automatically set focus on the input.

7. Add a field that will capture the question's content:
```tsx
<Fieldset>
	<FieldContainer>
	...
	</FieldContainer>
	<FieldContainer>
		<FieldLabel htmlFor="content">
			Content
		</FieldLabel>
		<FieldTextArea
			id="content"
			name="content"
			ref={register}
		/>
	</FieldContainer>
</Fieldset>
```

8. Finally, add a button for submitting the question using the `FormButtonContainer` and `PrimaryButton` *styled* components:
```jsx
<Fieldset>
	<FieldContainer>
	...
	</FieldContainer>
	<FormButtonContainer>
		<PrimaryButton type="submit">
			Submit Your Question
		</PrimaryButton>
	</FormButtonContainer>
</Fieldset>
```
9. Start the app by running the `npm start` command in Visual Studio Code's Terminal.
10. Let's give this a try in the running app by clicking the *Ask a question* button on the home page. Our form renders as expected.

React Hook Form and the styled form components made that job pretty easy. Now, let's try implementing another form, which is none other than the answer form.

# Implementing the answer form

Let's implement an answer form on the question page. Follow these steps:
1. Open *QuestionPage.tsx* and update the following import statement:
```tsx
import {
	gray3,
	gray6,
	Fieldset,
	FieldContainer,
	FieldLabel,
	FieldTextArea,
	FormButtonContainer,
	PrimaryButton,
} from './Styles';
```

2. Add an import statement for React Hook Form:
```tsx
import { useForm } from 'react-hook-form';
```

3. Add a type that will represent the form data:
```tsx
type FormData = {
	content: string;
};
```
4. Pass the form data type to the `useForm` hook and destructure the register function from it:
```tsx
export const QuestionPage = () => {
	...
	const { register } = useForm<FormData>();
	return ...
}
```
5. Let's create our form in the JSX, just beneath the list of answers:
```tsx
<AnswerList data={question.answers} />
<form
	css={css`
		margin-top: 20px;
	`}
>
	<Fieldset>
		<FieldContainer>
			<FieldLabel htmlFor="content">
				Your Answer
			</FieldLabel>
			<FieldTextArea
				id="content"
				name="content"
				ref={register}
			/>
		</FieldContainer>
		<FormButtonContainer>
			<PrimaryButton type="submit">
				Submit Your Answer
			</PrimaryButton>
		</FormButtonContainer>
	</Fieldset>
</form>
```

So, the form will contain a single field for the answer content and the submit button will have the caption *Submit Your Answer*.

6. Let's give this a try in the running app by clicking a question on the home page. Our form renders as expected.

We have now built three forms with React Hook Form and experienced first-hand how it simplifies building fields. We also built a handy set of styled form components along the way.

Our forms are looking good, but there is no validation yet. For example, we could submit a blank answer to a question, and it wouldn't be verified since there no such mechanism has been implemented yet. We will enhance our forms with validation in the next section. 

# Implementing validation 

Including validation on a form improves the user experience as you can provide immediate feedback on whether the information that's been entered is valid. In this section, we are going to add validation rules to the forms for asking and answering questions. These validation rules will include checks to ensure a field is populated and that it contains a certain number of characters.

## Implementing validation on the ask form

We are going to implement validation on the ask form by following these steps:
1. In *AskPage.tsx*, we are going to make sure that the title and content fields are populated by the user with a minimum number of characters. First, import the `FieldError` styled component:
```tsx
import {
	...
	FieldError,
} from './Styles';
```
2. Destructure the form error messages from the `useForm` hook:
```tsx
  const { register, control } = useForm<FormData>();
```
3. Next, configure the form so that it invokes the validation rules when the field elements lose focus (that is, the element's `blur` event):
```tsx
  const { register, control } = useForm<FormData>({
    // configure the form so that it invokes the validation rules when the field elements lose focus
    mode: "onBlur",
  });
  
  const { errors } = useFormState({ control });
```

> It is important to note that the fields will be validated when the form is submitted as well.

4. Specify the validation rules in the register function, as follows:
```tsx
<FieldInput
	id="title"
	type="text"
	{...register("title", { required: true, minLength: 10 })}
```

The highlighted code states that the title field is required and must be at least 10 characters long.

5. Let's conditionally render the validation error messages for both these rules beneath the input element:
```tsx
	<FieldInput
		...
	/>
	{errors.title && errors.title.type === 'required' && (
		<FieldError>
			You must enter the question title
		</FieldError>
	)}
	{errors.title && errors.title.type === 'minLength' && (
		<FieldError>
			The title must be at least 10 characters
		</FieldError>
	)}
</FieldContainer>
```

> `errors` is an object that React Hook Form maintains for us. The keys in the object correspond to the `name` property in `FieldInput`. The `type` property within each error specifies which rule the error is for.

6. Add validation to the content field to make it mandatory. It should contain at least 50 characters:
```tsx
<FieldTextArea
	id="content"
	{...register("content", { required: true, minLength: 50 })}/>
```

7. Add a validation error message to the content field:
```tsx
	<FieldTextArea
	...
	/>
	{errors.content && errors.content.type === 'required' && (
		<FieldError>
			You must enter the question content
		</FieldError>
	)}
	{errors.content && errors.content.type === 'minLength' && (
		<FieldError>
			The content must be at least 50 characters
		</FieldError>
	)}
</FieldContainer>
```

> The errors object will contain a content property when the content field fails a validation check. The type property within the content property indicates which rule has been violated. 

So, we use this information in the errors object to render the appropriate validation messages.

8. Let's give this a try. In the running app, go to the ask page by clicking on the *Ask a question* button at the bottom of the home screen.
Without entering anything in the form, click into and out of the fields. You'll see that the form has rendered validation errors, meaning the mechanism that we implemented has worked successfully. Don't type anything in the title field and then enter content that is less than 50 characters. Here, we can see that the validation errors render as we tab out of the fields.

# Implementing validation on the answer form

Let's implement validation on the answer form. We are going to validate that the content has been filled in with at least 50 characters. To do this, follow these steps:
1. Open *QuestionPage.tsx* and import the `FieldError` styled component:
```tsx
import {
	...
	FieldError,
} from './Styles';
```

2. In the `useForm` hook, destructure the `errors` object and configure the form to validate when its fields lose focus:
```tsx
const { register, control } = useForm<FormData>({
	mode: 'onBlur',
});

const { errors } = useFormState({ control });
```

3. Specify the validation rules on the answer field, as follows:
```tsx
<FieldTextArea
	id="content"
	{...register("content", { required: true, minLength: 50 })}
/>
```

Here, we've specified that the answer needs to be mandatory and must be at least 50 characters long.

4. Let's conditionally render the validation error messages for both these rules beneath the text area element:
```tsx
	<FieldTextArea
		...
	/>
	{errors.content && errors.content.type === 'required' && (
		<FieldError>
			You must enter the answer
		</FieldError>
	)}
	{errors.content && errors.content.type === 'minLength' && (
		<FieldError>
			The answer must be at least 50 characters
		</FieldError>
	)}
</FieldContainer>
```

5. In the running app, we can check that this is working as expected by clicking on a question on the home page and entering Some answer.

With that, we've finished implementing validation on our forms. React Hook Form has a useful set of validation rules that can be applied to its register function. The errors object from React Hook Form gives us all the information we need to output informative validation error messages. 

> More information on React Hook Form validation can be found at https://react-hook-form.com/get-started#Applyvalidation.

# Submitting forms

Submitting the form is the final part of the form's implementation. We are going to implement form submission logic in all three of our forms, starting with the search form.

**Submission logic** is logic that performs a task with the data from the form. Often, this task will involve posting the data to a web API to perform a server-side task, such as saving the data to a database table. In this section, our submission logic will simply call functions that will simulate web API calls.

## Implementing form submission in the search form

In *Header.tsx*, carry out the following steps to implement form submission on the search form:
1. Import the `useNavigate` hook from React Router:
```tsx
import {
	Link,
	useSearchParams,
	useNavigate,
} from 'react-router-dom';
```

2. Inside the `Header` component, on the first line, assign a function to the result of the `useNavigate` hook:
```tsx
const navigate = useNavigate();
```
3. Destructure a `handleSubmit` function from the `useForm` hook:
```tsx
const { register, handleSubmit } = useForm<FormData>();
```
This conflicts with the existing handleSubmit, which we'll resolve later in step 5.

4. Use the `handleSubmit` function to handle the submit event in the form:
```tsx
<form onSubmit={handleSubmit(submitForm)}>
```

The `handleSubmit` function from React Hook Form includes boilerplate code such as stopping the browser posting the form to the server.
Notice that we have passed `submitForm` to `handleSubmit`. This is a function that we will implement next that contains our submission logic.

5. Overwrite the existing `handleSubmit` function with the following submitForm function:
```tsx
const submitForm = ({ search }: FormData) => {
	navigate(`search?criteria=${search}`);
};
```

React Hook Form passes the function the form data. We destructure the `search` field value from the form data. The submission logic programmatically navigates to the search page, setting the `criteria` query parameter to the search field value.

Let's try this out in the running app. In the search box, enter the word *typescript* and press Enter. The browser location query parameter is set as expected, with the correct result rendering in the search form.
That's the submission implemented in our first form. Now, we will continue to implement the submission logic in our other forms.

## Implementing form submission in the ask form

Let's carry out the following steps to implement submission in the ask form:

1. In *QuestionsData.ts*, create a function that will simulate posting a question:
```tsx
export interface PostQuestionData {
	title: string;
	content: string;
	userName: string;
	created: Date;
}

export const postQuestion = async (question: PostQuestionData): Promise<QuestionData | undefined> => {
	await wait(500);
	const questionId = Math.max(...questions.map(q => q.questionId)) + 1;
	const newQuestion: QuestionData = {
		...question,
		questionId,
		answers: [],
	};

	questions.push(newQuestion);
	return newQuestion;
};
```

This function adds the question to the questions array using the `Math.max` method to set `questionId` to the next number.

2. In *AskPage.tsx*, import the function we just added to *QuestionData.ts*:
```tsx
import { postQuestion } from './QuestionsData';
```

Also, import the `SubmissionSuccess` message styled component:
```tsx
import {
	...,
	SubmissionSuccess,
} from './Styles';
```

3. Add some state for whether the form has been successfully submitted within the `AskPage` component:
```tsx
const [
	successfullySubmitted,
	setSuccessfullySubmitted,
] = React.useState(false);
```

4. Destructure the `handleSubmit` function from the `useForm` hook:
```tsx
const {
	register,
	handleSubmit,
} = useForm<FormData>({mode: 'onBlur'});
```

5. Also, destructure `formState` from the `useForm` hook:
```tsx
const {
	register,
	handleSubmit,
	formState,
} = useForm<FormData>({mode: 'onBlur'});
```

> `formState` contains information such as whether the form is being submitted and whether the form is valid.

6. Use the `handleSubmit` function to handle the `submit` event in the form:
```tsx
<form onSubmit={handleSubmit(submitForm)}>
```

7. Create a `submitForm` function just above the component's `return` statement, as follows:
```tsx
const submitForm = async (data: FormData) => {
	const result = await postQuestion({
		title: data.title,
		content: data.content,
		userName: 'Fred',
		created: new Date()
	});
	setSuccessfullySubmitted(result ? true : false);
};
```

The preceding code calls the `postQuestion` function asynchronously, passing in the title and content from the form data with a hardcoded username and created date.

8. Disable the form if the submission is in progress or successfully completed:
```jsx
<Fieldset
	disabled={formState.isSubmitting || successfullySubmitted}
>
```

> `isSubmitting` is a flag within `formState` that indicates whether form submission is taking place.

> ![[zICO - Warning - 16.png]] You may notice an `isSubmitted` flag within `formState`. This indicates whether a form has been submitted and is `true`, **even if the form is invalid**. 
> This is why we use our own state (`successfullySubmitted`) to indicate that a valid form has been submitted.

9. After `FormButtonContainer` in the JSX, add the following submission success message:
```jsx
{successfullySubmitted && (
	<SubmissionSuccess>
		Your question was successfully submitted
	</SubmissionSuccess>
)}
```

This message is rendered once the form has been successfully submitted.

10. In the running app, on the home page, click the *Ask a question* button and fill out the question form. Then, click the *Submit Your Question* button.
The form is disabled during and after a successful submission, and we receive the expected success message.

## Implementing form submission in the answer form

Carry out the following steps to implement form submission in the answer form:

1. In *QuestionsData.ts*, create a function that simulates posting an answer:
```tsx
export interface PostAnswerData {
	questionId: number;
	content: string;
	userName: string;
	created: Date;
}

export const postAnswer = async (answer: PostAnswerData): Promise<AnswerData | undefined> => {
	await wait(500);
	const question = questions.filter(q => q.questionId === answer.questionId)[0];
	const answerInQuestion: AnswerData = {answerId: 99, ...answer};
	question.answers.push(answerInQuestion);
	return answerInQuestion;
};
```

The function finds the question in the questions array and adds the answer to it. The remainder of the preceding code contains straightforward types for the answer to post and the function's result.

2. In *QuestionPage.tsx*, import the function we just created, along with the `Styles` component for a success message:
```jsx
import {
	…,
	postAnswer
} from './QuestionsData';

import {
	…,
	SubmissionSuccess,
} from './Styles';
```

3. Add a state for whether the form has been successfully submitted in the `QuestionPage` component:
```tsx
const [
	successfullySubmitted,
	setSuccessfullySubmitted,
] = React.useState(false);
```

4. Destructure `handleSubmit` and `formState` from the `useForm` hook:
```jsx
const {
	register,
	handleSubmit,
	formState
} = useForm<FormData>({mode: 'onBlur'});
```

5. Use the `handleSubmit` function to handle the `submit` event in the form:
```jsx
<form
	onSubmit={handleSubmit(submitForm)}
	css={…}
>
```

6. Create a `submitForm` function just above the component's return statement, as follows:
```jsx
const submitForm = async (data: FormData) => {
	const result = await postAnswer({
		questionId: question!.questionId,
		content: data.content,
		userName: 'Fred',
		created: new Date(),
	});
	setSuccessfullySubmitted(result ? true : false);
};
```

So, this calls the `postAnswer` function, asynchronously passing in the content from the field values with a hardcoded username and the created date.

> ![[zICO - Warning - 16.png]] Notice `!` after the reference to the question state variable. This is a non-null assertion operator. 
> A **non-null assertion operator** (`!`) tells the TypeScript compiler that the variable before it cannot be `null` or `undefined`. This is useful in situations where the TypeScript compiler isn't smart enough to figure this fact out itself.

So, `!` in `question!.questionId` stops the TypeScript from complaining that question could be `null`.

7. Disable the form if the submission is in progress or successfully completed:
```tsx
<Fieldset
	disabled={formState.isSubmitting || successfullySubmitted}
>
```

8. After `FormButtonContainer` in the JSX, add the following submission success message:
```jsx
{successfullySubmitted && (
	<SubmissionSuccess>
		Your answer was successfully submitted
	</SubmissionSuccess>
)}
```

9. In the running app, on the home page, click on a question. Fill in an answer and submit it.
Like the ask form, the answer form is disabled during and after submission, and we receive the expected success message.

