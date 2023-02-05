#aspnet_core/react 

---

# Documentation

https://create-react-app.dev/docs/documentation-intro

# Creating the app with CRA

Let's create the React and TypeScript app with CRA by carrying out the following steps:
1. Open Visual Studio Code in the QandA folder we created earlier. Note that we should be at the same level as the backend project and not inside it.
2. Open the Terminal in Visual Studio Code, which can be found in the View menu or by pressing `Ctrl + '`. 
   Execute the following command in the Terminal:
   `npx create-react-app frontend --template typescript`
   The npx tool is part of npm that temporarily installs the *create-reactapp* npm package and uses it to create our project. We have told the *create-react-app* npm package to create our project in a folder called *frontend*. The `–-template typescript` option has created our React project with TypeScript.
3. If we look in the *src* folder, we'll see that the `App` component has a `tsx` extension. This means that this is a TypeScript component.
4. Let's verify whether the app runs okay by executing the following commands in the Terminal:
```
cd frontend
npm start
```
5. Press `Ctrl + C` to stop the running app and `Y` when you're asked to terminate the job.

- Folder Structure
https://create-react-app.dev/docs/folder-structure
- Debugin in the editor
https://create-react-app.dev/docs/setting-up-your-editor#debugging-in-the-editor


# Adding linting to React and TypeScript

So, why are we using Visual Studio Code to develop our React app and not Visual Studio? Well, the overall experience is a little better and faster when developing frontend code with Visual Studio Code.
So, we now have a React and TypeScript app using the latest version of CRA. In the next section, we are going to add more automated checks to our code by introducing linting into our project.

## Adding linting to React and TypeScript

*Linting* is a series of checks that are used to identify code that is potentially problematic. A *linter* is a tool that performs linting, and it can be run in our code editor as well as in the continuous integration (CI) process. So, linting helps us write consistent and highquality
code as it is being written.
**ESLint** is the most popular linter in the React community and has already been installed in our project for us by CRA. Due to this, we will be using ESLint as our linting tool for our app.

> ![[zICO - Exclamation - 16.png]] TSLint was a popular alternative to ESLint for linting TypeScript code but is now deprecated. More information can be found at https://medium.com/palantir/tslint-in-2019-1a144c2317a9.

In the following subsections, we will learn how to configure ESLints rules, as well as how  to configure Visual Studio Code to highlight violations.

### Configuring Visual Studio Code to lint TypeScript code

CRA has already installed ESLint and configured it for us.

> Note that ESLint doesn't appear in our *package.json* file. Instead, it is part of the CRA package. This can be confirmed by opening the *package.json* file in *node_modules\react-scripts*.

We need to tell Visual Studio Code to lint TypeScript code. Let's carry out the following steps to do this:
1. First, let's reopen Visual Studio Code in the *frontend* folder. This is required for an extension that we are going to install in a later step.
2. Go to the *Extensions* area in Visual Studio Code (`Ctrl + Shift + X`) and type `eslint` into the search box in the top-left corner. The extension we are looking for is called *ESLint* and is published by MIcrosoft.
3. Click on the *Install* button to install the extension.
4. Open *Settings* from the *Preferences* menu on the *File* menu. The shortcut keys to open Settings are `Ctrl + ,`.
5. Enter eslint in the search box and scroll down to the *Eslint: Probe* setting.
6. Make sure that *typescript* and *typescriptreact* are in the list. If not, add them using the *Add Item* button.

> The preceding screenshot shows the setting being added to all the projects for the current user because it is in the *User* tab. If we just want to change a setting in the current project, we can find it in the *Workspace* tab and adjust it.

## Configuring linting rules

Now that Visual Studio Code is linting our code, let's carry out the following steps to understand how we can configure the rules that ESLint executes:
1. Let's create a file called .eslintrc.json in the frontend folder with the following code:
```json
{
	// This file defines the rules that ESLint executes. We have just told it to execute all the rules that have been configured in CRA.
	"extends": "react-app"
}
```
2. Let's check that Visual Studio Code is linting our code by adding the following commented out line to *App.tsx*, just before the return statement:
```jsx
function App() {
  // const unused = 'something';
  return (	
	...
	);
};
```

We'll see that ESLint immediately flags this line as being unused. That's great – this means our code is being linted.
3. Now, let's add a rule that CRA hasn't been configured to apply. In the *.eslintrc.json* file, add the following highlighted lines:
```json
{
	"extends": "react-app",
	"rules": {
		"no-debugger":"warn"
	}
}
```

Here, we have told ESLint to warn us about the use of `debugger` statements.

> The list of available ESLint rules can be found at https://eslint.org/docs/rules/.

4. Let's add a `debugger` statement below our unused variable in App.tsx, like so:
```jsx
function App() {
  // debugger;
  return (	
	...
	);
};
```

# Adding automatic code formatting to React and TypeScript

Enforcing a consistent code style improves the readability of the code base, but it can be a pain, even if ESLint reminds us to do it. Wouldn't it be great if those semicolons we forgot to add to the end of our statements were just automatically added for us? Well, that is what automatic code formatting tools can do for us, and Prettier is one of these great tools.
We will start this section by installing *Prettier* before configuring it to work nicely with ESLint and Visual Studio Code.

## Adding Prettier

We are going to add Prettier to our project by following these steps in Visual Studio Code:
1. Make sure you are in the *frontend* directory. Execute the following command to install Prettier:
`npm install prettier --save-dev`
2. Now, we want Prettier to take responsibility for the style rules from ESLint. Let's install some npm packages that will do this:
`npm install eslint-config-prettier eslint-plugin-prettier --save-dev`
*eslint-config-prettier* disables ESLint rules that conflict with Prettier. *eslint-plugin-prettier* is an ESLint rule that formats code using Prettier.
3. Now, let's tell ESLint to let Prettier take care of the code formatting by adding the following changes to *.eslintrc.json*:
```json
{
    "extends": ["react-app","plugin:prettier/recommended"],
    "rules": {
        "prettier/prettier": [
            "error",
            {
                "endOfLine": "auto"
            }
        ],
        "no-debugger":"warn"    
    }
}
```

4. Now, let's specify the formatting rules we want in a *.prettierrc* file in the *frontend* folder. Create a file with the following content:
```json
{
	"printWidth": 80,
	"singleQuote": false,
	"semi": true,
	"tabWidth": 2,
	"trailingComma": "all",
	"endOfLine": "auto"
}
```

These rules will result in lines over 80 characters long being sensibly wrapped, double quotes being automatically converted into single quotes, semicolons being automatically added to the end of statements, indentations automatically being set to two spaces, and trailing commas being automatically added wherever possible to items such as arrays on multiple lines.
5. Now, go to the *Extensions* area in Visual Studio Code (`Ctrl + Shift + X`) and type *prettier* into the search box in the top-left corner. The extension we are looking for is called *Prettier – Code formatter* and is published by Esben Petersen.
6. Click on the *Install* button to install the extension.
7. We can get Prettier to format our code when a file is saved in Visual Studio Code with some settings. Open *Settings* from the Preferences menu on the *File* menu. Enter *format* into the search box and make sure *Default Formatter* is set to *esbenp.prettier-vscode* and *Format on Save* is ticked.

So, that's Prettier set up. Whenever we save a file in Visual Studio Code, it will be automatically formatted.

---

• ASP.NET Core API controllers: https://docs.microsoft.com/en-us/aspnet/core/web-api
• npx: https://www.npmjs.com/package/npx
• Create React app: https://create-react-app.dev/docs/gettingstarted
• ESLint: https://eslint.org/
• Prettier: https://prettier.io/