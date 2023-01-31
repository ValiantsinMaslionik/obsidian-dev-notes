#react

---

# Creating data models

We desperately need some data so that we can develop our frontend. In this section, we'll create some mock data in our frontend. We will also create a function that components will call to get data. Eventually, this function will call our real ASP.NET Core backend.
Follow these steps:
1. Create a new file in the *src* folder called *QuestionsData.ts* with the following interface:
```ts
export interface QuestionData {
	questionId: number;
	title: string;
	content: string;
	userName: string;
	created: Date;
}
```

Before moving on, let's understand the code we have just entered since we have just written some TypeScript.

> An `interface` is a type that defines the structure for an object, including all its properties and methods. **Interfaces don't exist in JavaScript**, so they are purely used by the *TypeScript* compiler during the type checking process. We create an interface with the `interface` keyword, followed by its name, followed by the properties and methods that make up the interface in curly braces. More information can be found at https://www.typescriptlang.org/docs/handbook/interfaces.html.

So, our interface is called `QuestionData` and it defines the structure of the questions we expect to be working with. We have exported the interface so that it can be used throughout our app when we interact with question data.

Notice what appear to be types after the property names in the interface. These are called *type annotations* and is a **TypeScript feature that doesn't exist in JavaScript**.

> Type annotations lets us declare variables, properties, and function parameters with specific types. This allows the TypeScript compiler to check that the code adheres to these types. In short, type annotations allow TypeScript to catch bugs where our code is using the wrong type much earlier than if we were writing our code in JavaScript.

Notice that we have specified that the created property has a `Date` type. 

> The `Date` type is a special type in TypeScript that represents the `Date` JavaScript object. This `Date` object represents a single moment in time and is specified as the number of milliseconds since midnight on January 1,1970, UTC. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date.

2. Under `QuestionData`, let's create another interface for the structure of the answers we expect:
```ts
export interface AnswerData {
	answerId: number;
	content: string;
	userName: string;
	created: Date;
}
```

3. Now, we can adjust the `QuestionData` interface so that it includes an array of answers:
```ts
export interface QuestionData {
  questionId: number;
  title: string;
  content: string;
  userName: string;
  created: Date;
  answers: AnswerData[];
}
```

Notice the square brackets in the type annotation for the answers property.

> Square brackets after a type denote **an array of the type**. More information can be found at https://www.typescriptlang.org/docs/handbook/basic-types.html#array.

# Adding mock data

Let's create some mock questions below the interfaces. You can copy the code at https://github.com/PacktPublishing/NET-5-and-React-17-Second-Edition/blob/master/chapter-03/finish/frontend/src/QuestionsData.ts to save yourself typing it all out:
```ts
const questions: QuestionData[] = [
{
    questionId: 1,
    title: "Why should I learn TypeScript?",
    content: "TypeScript seems to be getting popular so I wondered whether it is worth my time learning it? What benefits does it give over JavaScript?",
    userName: "Bob",
    created: new Date(),
    answers: [
      {
        answerId: 1,
        content: "To catch problems earlier speeding up your developments",
        userName: "Jane",
        created: new Date(),
      },
      {
        answerId: 2,
        content: "So, that you can use the JavaScript features of tomorrow, today",
        userName: "Fred",
        created: new Date(),
      },
    ],
  },
  {
    questionId: 2,
    title: "Which state management tool should I use?",
    content: "There seem to be a fair few state management tools around for React - React, Unstated, ...Which one should I use?",
    userName: "Bob",
    created: new Date(),
    answers: [],
  },
];
```

Notice that we typed out our questions variable, which contains the array of the `QuestionData` interface we have just created. If we miss a property out or misspell it, the TypeScript compiler will complain.

5. Let's create a function that returns unanswered questions:
```ts
export const getUnansweredQuestions = (): QuestionData[] => {
  return questions.filter((q) => q.answers.length === 0);
};
```

This function returns the question array items we have just created, which contains no answers, by making use of the `array.filter` method.

> The `array.filter` method in an array executes the function that was passed into it for each array item, and then creates a new array with all the elements that return truthy from the function. A truthy value is any value other than `false`, `0`, `""`, `null`, `undefined`, or `NaN`. More information can be found at https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter.

Notice that we defined the return type, `QuestionData[]`, for the function after the function parameters.
