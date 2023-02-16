#react

---

In this section, we're going to style the body, app container, and header container with regular CSS and understand the drawbacks of this approach.

# Styling the document body

We are going to use the traditional approach to style the document's body. Follow these steps to do so:
1. Open *index.tsx*. **Remember that index.tsx is the root of the React component tree**. Notice how the CSS file is referenced:
```tsx
import './index.css';
```

To reference a CSS file in a React component, we specify the location of the file after the import statement. *index.css* is in the same folder as *index.tsx*, so the import path is `./`.
2. Open *index.css*. Notice that we already have CSS in place for the body tag. Let's remove everything apart from margin and add a background color:
```css
body {
	margin: 0;
	background-color: #f7f8fa;
}
```

The background color of the app is now light gray. Leave the app running as we progress to styling our App component.

# Styling the App component

We are going to apply a CSS class to the `App` component in the following steps: 1. Open *App.tsx* and change the class name to container on the `div` element:
```tsx
function App() {
	return (
		<div className="container">
			<Header />
			124 Styling React Components with Emotion
			<HomePage />
		</div>
	);
}
```

> ![[zICO - Warning - 16.png]] Why is a `className` attribute used to reference CSS classes? Shouldn't we use the `class` attribute? Well, we already know that JSX compiles down to JavaScript, and since `class` is a keyword in JavaScript, React uses a `className` attribute instead. React converts `className` to class when it adds elements to the HTML DOM.

The React team is currently working on allowing `class` attributes to be used instead of `className`. See https://github.com/facebook/react/issues/13525 for more information.

2. Open *App.css*, delete all the CSS in this file, and add the following CSS class:
```css
.container {
	font-family: 'Segoe UI', 'Helvetica Neue', sans-serif;
	font-size: 16px;
	color: #5c5a5a;
}
```

3. Look at the app in the browser. We see that the text content within the app has the font, size, and color we specified. Leave the app running while we apply more CSS in the next subsection.

# Styling the Header component

We are going to apply a CSS class to the `Header` component in the following steps:
1. Create a file called *Header.css* in the *src* folder and add the following CSS class:
```css
.container {
	position: fixed;
	box-sizing: border-box;
	top: 0;
	width: 100%;
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 10px 20px;
	background-color: #fff;
	border-bottom: 1px solid #e3e2e2;
	box-shadow: 0 3px 7px 0 rgba(110, 112, 114, 0.21);
}
```

This style will fix the element it is applied to the top of the page. The elements within it will flow horizontally across the page and be positioned nicely.
2. Open *Header.tsx* and import the CSS file we just created:
```tsx
import './Header.css';
```

3. Add a `container` class to the `div` element in the JSX:
```tsx
<div className="container">
```

5. We can see that the container style has leaked out of the `Header` component into the `App` component because the `div` element in the `App` component has a drop shadow.
6. Press `Ctrl + C` in the terminal and press `Y` if prompted to stop the app from running.

We have just experienced a common problem with CSS. CSS is global in nature. So, if we use a CSS class name called `container` within a component, it would collide with another CSS class called `container` in a different CSS file if a page references both CSS files.
We can resolve this issue by being careful when naming and structuring our CSS by using something such as **BEM**. For example, we could have named the CSS classes `app-container` and `header-container` so that they don't collide. However, there's still some risk of the chosen names colliding, particularly in large apps and when new members join a team.

> **BEM** stands for *Block, Element, Modifier* and is a popular naming convention for CSS class names. More information can be found at https://csstricks.com/bem-101/.

# Styling components with CSS modules

**CSS modules** are a mechanism for scoping CSS class names. The scoping happens as a build step rather than in the browser. In fact, CSS modules are already available in our app because CRA has configured them in webpack.
We are going to update the styles on the `Header` and `App` components to CSS modules in the following steps:
1. Rename *Header.css* to *Header.module.css* and *App.css* to *App.module.css*. **Create React App is configured to treat files ending with `module.css` as CSS modules**.
2. Open *App.tsx* and change the *App.css* import statement to the following:
```tsx
import styles from './App.module.css'
```
3. Update the `className` prop on the `div` element to the following:
```tsx
<div className={styles.container}>
```
This references the container class with the `App` CSS module.
4. Open *Header.tsx* and change the *Header.css* import statement to the following:
```tsx
import styles from './Header.module.css'
```
5. Update the `className` prop on the `div` element to the following:
```tsx
<div className={styles.container}>
```
6. Let's run the app by entering the following command in the terminal:
`npm start`

We can see that styles from the `Header` component no longer leak into the `App` component. We can see that the CSS modules have updated the class names on the elements, prefixing them with the component name and adding random-looking characters to the end.
8. Look in the HTML head tag. We can see the CSS from the CSS modules in style tags.
9. Press `Ctrl + C` in the terminal and press `Y` if prompted to stop the app from running.

This is great! CSS modules automatically scope CSS that is applied to React components without us having to be careful with the class naming.

# Styling components with Emotion

In this section, we're going to style the `App`, `Header`, and `HomePage` components with a popular CSS-in-JS library called **Emotion**. Along the way, we will discover the benefits of CSS-in-JS over CSS modules.

### Installing Emotion

With our frontend project open in Visual Studio Code, let's install *Emotion* into our project by carrying out the following steps:
1. Open the terminal, make sure you are in the *frontend* folder, and execute the following command:
`npm install @emotion/react @emotion/styled`

There is a nice Visual Studio Code extension that will provide CSS syntax highlighting and IntelliSense for Emotion. Open the Extensions area (`Ctrl + Shift + X` on Windows or `Cmd + Shift + X` on Mac) and type `styled components` in the search box at the top left. The extension we are looking for is called `vscodestyled-components` and was published by Julien Poissonnier.

> This extension was primarily developed for the *Styled Components CSS* in the JS library. CSS highlighting and IntelliSense work for Emotion as well, though.

> To show the Explorer panel again, press `Ctrl + Shift + E` on Windows or `Cmd + Shift + E` on Mac.

## Styling the App component

Let's style the `App` component with Emotion by carrying out the following steps:
1. Start by removing the *App.module.css* file by right-clicking on it and selecting the *Delete* option.
2. In *App.tsx*, remove the line that says import styles from '*./App.module.css*'.
3. Remove the React `import` statement from *App.tsx*.

> ![[zICO - Warning - 16.png]] In React 17 and beyond, it is no longer necessary to include the React `import` statement to render JSX, as is the case in *App.tsx*. 
> However, the React `import` statement is still required if we want to use functions from the library, such as `useState` and `useEffect`.

4. Add the following imports from the Emotion library at the top of *App.tsx*:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
```

The `css` function is what we'll use to style an HTML element. 
The comment above the import statement tells *Babel* to use this jsx function to transform JSX into JavaScript.

> ![[zICO - Warning - 16.png]] It is important to include the `/** @jsxImportSource @emotion/react */` comment; otherwise, the transpilation process will error out. 
> It is also important that this is placed **right at the top of the file**.

5. On the `App` component's `div` element, remove the `className` prop and use the `css` function to specify the style:
```tsx
<div css={css`
	font-family: 'Segoe UI', 'Helvetica Neue', sans-serif;
	font-size: 16px;
	color: #5c5a5a;
`}>
	<Header />
	<HomePage />
</div>
```

We put the styles in a `css` prop on an HTML element in what is called a **tagged template literal**.

> A **template literal** is a string enclosed by backticks (\`\`) that can span multiple lines and can include a JavaScript expression in curly braces, prefixed with a dollar sign (`${expression}`). 
> Template literals are great when we need to merge static text with variables.

> A **tagged template literal** is a template string that is executed through a function that is specified immediately before the template literal string. 
> The function is executed on the template literal before the string is rendered in the browser.

So, Emotion's `css` function is being used in a **tagged template literal** to render the styles defined in backticks (\`\`) on the HTML element.

We actually want to specify the `font family`, `size`, and `color` in various components in our app. To do this, we are going to extract these values into variables in a separate file. 
1. Let's create a file called *Styles.ts* in the *src* folder that contains the following variables:
```ts
export const gray1 = '#383737';
export const gray2 = '#5c5a5a';
export const gray3 = '#857c81';
export const gray4 = '#b9b9b9';
export const gray5 = '#e3e2e2';
export const gray6 = '#f7f8fa';
export const primary1 = '#681c41';
export const primary2 = '#824c67';
export const accent1 = '#dbb365';
export const accent2 = '#efd197';
export const fontFamily = "'Segoe UI', 'HelveticaNeue',sans-serif";
export const fontSize = '16px';
```

Here, we have defined six shades of gray, two shades of the primary color for our app, two shades of an accent color, as well as the font family we'll use with the standard font size.

7. Let's import the variables we need into *App.tsx*:
```tsx
import { fontFamily, fontSize, gray2 } from './Styles';
```
8. Now, we can use these variables inside the CSS template literal using interpolation:
```tsx
<div
	css={css`
		font-family: ${fontFamily};
		font-size: ${fontSize};
		color: ${gray2};
	`}
>
	<Header />
	<HomePage />
</div>
```

Congratulations – we have just styled our first component with Emotion!
Let's explore the styling in the running app. This will help us understand how Emotion is applying styles:
1. Let's run the app by executing `npm start` in the terminal.
2. Let's inspect the DOM on the browser page by pressing F12.

We can see that the `div` element we styled has a class name that starts with *css* and ends with the component name, with a unique name in the middle. The styles in the CSS class are the styles we defined in our component with Emotion. **So, Emotion doesn't generate inline styles as we might have thought. Instead, Emotion generates styles that are held in unique CSS classes**. If we look in the HTML header, we'll see the CSS class defined in a `style` tag.
So, during the app's build process, Emotion has transformed the styles into a real CSS class.

**An advantage of using Emotion over CSS modules is the ability to use variables in CSS**. We have just used this to define some common style properties and use them in the `App` component. We will use variables in styles throughout this book where logic drives styles on components.

## Styling the Header component

We can style the `Header` component with *Emotion* by carrying out the following steps:
1. Start by removing the *Header.module.css* file.
2. In *Header.tsx*, remove the *Header.module.css* import statement and add the following imports for Emotion and some of the style variables we set up previously. Add these import statements right at the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
import { fontFamily, fontSize, gray1, gray2, gray5 } from './Styles';
```

3. Now, we can remove the `className` property and define the following style on the `div` container element:
```tsx
<div
	css={css`
		position: fixed;
		box-sizing: border-box;
		top: 0;
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 10px 20px;
		background-color: #fff;
		border-bottom: 1px solid ${gray5};
		box-shadow: 0 3px 7px 0 rgba(110, 112, 114, 0.21);
	`}
>
...
</div>
```

4. The `App` and `Header` components are now styled as they were with the CSS modules approach. In the next section, we will continue to style the `Header` component, learning about more features in Emotion.

## Styling pseudo-classes and nested elements with Emotion

In this section, we will learn how to style pseudo-classes with Emotion. We will then move on to learn how to style nested elements:
1. In *Header.tsx*, implement the following styles on the `anchor` tag:
```tsx
<a
	href="./"
	css={css`
		font-size: 24px;
		font-weight: bold;
		color: ${gray1};
		text-decoration: none;
	`}
>
Q & A
</a>
```

Here, we are making the app name fairly big, bold, and dark gray, and also removing the underline.

2. Let's move on and style the search box:
```tsx
<input
	type="text"
	placeholder="Search..."
	onChange={handleSearchInputChange}
	css={css`
		box-sizing: border-box;
		font-family: ${fontFamily};
		font-size: ${fontSize};
		padding: 8px 10px;
		border: 1px solid ${gray5};
		border-radius: 3px;
		color: ${gray2};
		background-color: white;
		width: 200px;
		height: 30px;
	`}
/>
```

Here, we are using the standard font family and size and giving the search box a light-gray, rounded border.

3. We are going to continue to style the `input` element. We want to change its outline color for when it has focus to a gray color. We can achieve this with a `focus` pseudo-class selector, as follows:
```tsx
<input
	type="text"
	placeholder="Search..."
	css={css`
		…
		:focus {
			outline-color: ${gray5};
		}
	`}
/>
```

The pseudo-class is defined by being nested within the CSS for the `input`. The syntax is the same as in regular CSS with a colon (`:`) before the pseudo-class name and its CSS properties within curly brackets.

4. Let's move on to the *Sign In* link element now. Let's start to style it as follows:
```tsx
<a
	href="./signin"
	css={css`
		font-family: ${fontFamily};
		font-size: ${fontSize};
		padding: 5px 10px;
		background-color: transparent;
		color: ${gray2};
		text-decoration: none;
		cursor: pointer;
		:focus {
			outline-color: ${gray5};
		}
	`}
>
<UserIcon />
<span>Sign In</span>
</a>
```

The styles have added some space around the icon and the text inside the link and removed the underline from it. We have also changed the color of the line around the link when it has focus using a pseudo-class selector.

5. Let's style the `span` element within the *Sign In* link by adding the following to the end of the template literal:
```tsx
<a
	href="./signin"
	css={css`
		…
		span {
			margin-left: 7px;
		}
	`}
>
	<UserIcon />
	<span>Sign In</span>
</a>
```

We have chosen to use **a nested element selector** on the `anchor` tag to style the `span` element. This is equivalent to applying the style directly on the span element, as follows:
```tsx
<span
	css={css`
		margin-left: 7px;
	`}
>
	Sign In
</span>
```

6. Next up is styling the `UserIcon` component in the *Icons.tsx* file. Let's add the following to the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
```
7. Now, we can define the styles on the img element, replacing the width attribute:
```tsx
<img
	src={user}
	alt="User"
	css={css`
		width: 12px;
		opacity: 0.6;
	`}
/>
```

We've moved the `width` from the attribute on the `img` tag into its CSS style. Now, the icon is a nice size and appears to be a little lighter in color. 

We are getting the hang of Emotion now. The syntax for defining the styling properties is exactly the same as defining properties in CSS, which is nice if we already know CSS well. We can even nest CSS properties in a similar manner to how we can do this in SCSS.
The remaining component to style is `HomePage` – we'll look at that next.

## Creating a reusable styled component with Emotion

In this section, we are going to learn how to create reusable styled components while
styling the HomePage component. Let's carry out the following steps:
1. We will start with the `Page` component in *Page.tsx* and add the following lines to the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
```
2. Let's style the `div` element and place the page content in the center of the screen:
```tsx
export const Page = ({ title, children }: Props) => (
	<div
		css={css`
			margin: 50px auto 20px auto;
			padding: 30px 20px;
			max-width: 600px;
		`}
	>
		…
	</div>
);
```
3. Let's move to *HomePage.tsx* and add the following lines to the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
```
4. Next, we will style the `div` element that wraps the page title and the *Ask a question* button:
```tsx
<Page>
	<div
		css={css`
			display: flex;
			align-items: center;
			justify-content: space-between;
		`}
	>	
		<PageTitle>Unanswered Questions</PageTitle>
		<button onClick={handleAskQuestonClick}>Ask a question</button>
	</div>
</Page>
```
This puts the page title and the *Ask a question* button on the same line.

5. Now, let's open *PageTitle.tsx* and style the page title. Add the following lines to the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
```
6. We can then apply the following styles to the `h2` element:
```tsx
<h2
	css={css`
		font-size: 15px;
		font-weight: bold;
		margin: 10px 0px 5px;
		text-align: center;
		text-transform: uppercase;
	`}
>
	{children}
</h2>
```

This reduces the size of the page title and makes it uppercase, which will make the page's content stand out more.

Finally, we have the *Ask a question* button, which is the primary button on the home page. Eventually, we are going to have primary buttons on several pages, so let's create a reusable `PrimaryButton` styled component in the *Styles.ts* file.
 
1. First, we need to import the `styled` function from Emotion. So, add this line to the top of the file:
```ts
import styled from '@emotion/styled';
```
2. Now, we can create the primary button `styled` component:
```ts
export const PrimaryButton = styled.button`
	background-color: ${primary2};
	border-color: ${primary2};
	border-style: solid;
	border-radius: 5px;
	font-family: ${fontFamily};
	font-size: ${fontSize};
	padding: 5px 10px;
	140 Styling React Components with Emotion
	color: white;
	cursor: pointer;
	:hover {
		background-color: ${primary1};
	}
	:focus {
		outline-color: ${primary2};
	}
	:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}
`;
```

Here, we've created a styled component in Emotion by using a **tagged template literal**.

> **A tagged template literal** is a template literal to be parsed with a function. The template literal is contained in backticks (``) and the parsing function is placed immediately before it. 
> More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals.

In a styled component, the parsing function before the backticks (\`\`) references a function within Emotion's `styled` function. The function is named with the HTML element name that will be created and is styled with the provided style. This is button in this example.

So, this styled component creates a flat, slightly rounded button with our chosen primary color.

3. Let's import this into the *HomePage.tsx* file:
```tsx
import { PrimaryButton } from './Styles';
```
10. Now, we can replace the `button` tag in the HomePage JSX with our `PrimaryButton` styled component:
```tsx
<Page>
	<div ... >
		<PageTitle>…</PageTitle>
		<PrimaryButton onClick={handleAskQuestionClick}>Ask a question</PrimaryButton>
	</div>
</Page>
```

There is still work to do on the home page's styling, such as rendering the list of unanswered questions. We'll do this in the next section.

# Styling the QuestionList component

Let's go through the following steps to style the `QuestionList` component:
1. Open *QuestionList.tsx* and add the following lines at the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from "@emotion/react";
```
2. Also import the following common styles:
```tsx
import { accent2, gray5 } from './Styles';
```
3. Style the ul element in the JSX as follows:
```tsx
<ul
	css={css`
		list-style: none;
		margin: 10px 0 0 0;
		padding: 0px 20px;
		background-color: #fff;
		border-bottom-left-radius: 4px;
		border-bottom-right-radius: 4px;
		border-top: 3px solid ${accent2};
		box-shadow: 0 3px 5px 0 rgba(0, 0, 0, 0.16);
	`}
>
	…
</ul>
```

So, the `ul` element will appear without bullet points and with a rounded border.
The top border will be slightly thicker and in the accent color. We've added a box shadow to make the list pop out a bit.

4. Now, let's style the list items:
```tsx
<ul ...
>
	<li key={question.questionId}
		css={css`
			border-top: 1px solid ${gray5};
			:first-of-type {
			border-top: none;
		}
	`}
	>
		…
	</li>
</ul>
```

The style on the list items adds a light-gray top border. This will act as a line separator between each list item.

# Styling the Question component

Follow these steps to style the `Question` component:
1. Open *Question.tsx* and add the following lines to the top of the file:
```tsx
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';
import { gray2, gray3 } from './Styles';
```
2. Let's add some padding to the outermost `div` element, as follows:
```tsx
<div
	css={css`
		padding: 10px 0px;
	`}
>
	<div>
		{data.title}
	</div>
	…
</div>
```
3. Add some padding to the question title and increase its font size:
```tsx
<div
	css={css`
		padding: 10px 0px;
		font-size: 19px;
	`}
>
	{data.title}
</div>
```
4. Add the following styles to the optional question content:
```tsx
{showContent && (
	<div
		css={css`
			padding-bottom: 10px;
			font-size: 15px;
			color: ${gray2};
		`}
	>
		…
	</div>
)}
```
5. Lastly, add the following styles around the text for who asked the question:
```tsx
<div
	css={css`
		font-size: 12px;
		font-style: italic;
		color: ${gray3};
	`}
>
	{`Asked by ${data.userName} on	${data.created.toLocaleDateString()} ${data.created.toLocaleTimeString()}`}
</div>
```