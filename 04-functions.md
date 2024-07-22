# 4. Functions and Methods

**This chapter covers**

- Enhancing type safety with overloaded function signatures done properly

- Specifying return types of functions

- Using rest parameters (...) in functions correctly

- Grasping the essence of this and globalThis in functions with the support of bind, apply, call and StrictBindCallApply

- Handling function types safely

- Employing utility types ReturnType, Parameters, Partial, ThisParameterType and OmitThisParameter for functions

Alright, brace yourself for a deep dive into the functional world of TypeScript and JavaScript. Why are we focusing on functions, you ask? Well, without functions, JavaScript and TypeScript would be as useless as a chocolate teapot. So, let's get down to business---or should I say "fun"ction? Eh, no? I promise the jokes will get better!

Now, just like an Avengers movie without a post-credit scene, JavaScript and TypeScript without functions would leave us in quite a despair. TypeScript, being the older, more sophisticated sibling, brings to the table a variety of function flavors that make coding more than just a mundane chore.

First off, we have the humble function declaration, the JavaScript original that TypeScript inherited:

```typescript
function greet(name) {
  console.log(`Hello, ${name}!`);
}

greet("Tony Stark");      // #A
```

`#A Logs: "Hello, Tony Stark!"`

Then TypeScript, in its pursuit of stricter typing, added types to parameters and return values:

```typescript
function greet(name: string): void {
  console.log(`Hello, ${name}!`);
}

greet("Peter Parker");    // #A
```

`#A Logs: "Hello, Peter Parker!"`

By the way, to compliment a TypeScript function just tell it that it's very _call_-able.

And we also have a concept of hoisted functions. Function hoisting in JavaScript is a behavior where function declarations are moved to the top of their containing scope during the compile phase, before the code has been executed. This is why you can call a function before it's been declared in your code. However, only function declarations are hoisted, not function expressions.

```typescript
hoistedFunction();        // #A

function hoistedFunction(): void {  
  console.log("Hello, I have been hoisted!");
}
```

`#A Outputs: "Hello, I have been hoisted!"`

Function expressions in JavaScript are a way to define functions as an expression, meaning the function can be assigned to a variable, stored in an object, or passed as an argument to other functions.

Unlike function declarations which are hoisted to the top of their scope, function expressions are not hoisted, which means you can't call a function expression before it's been defined in your code.

Here's a simple example of a function expression:

```typescript
let greet = function (): void {
  console.log("Hello, world!");
};

greet();                  // #A
```

`#A Outputs: "Hello, world!"`

And let's not forget the charming arrow functions that take us to ES6 nirvana. Short, sweet, and this-bound, they're the Hawkeye of the TypeScript world:

```typescript
const greet = (name: string): void => {
  console.log(`Hello, ${name}!`);
};

greet("Bruce Banner");    // #A
```

`#A Logs: "Hello, Bruce Banner!"`

Function expressions can also be used as callbacks parameters to other functions or as immediately invoked function expressions (IIFE) without being assigned to a variable. This is often used to create a new scope and avoid polluting the global scope for a module or library:

```typescript
(function (): void {
  const message = "Hello, world!";

  console.log(message);   // #A
})();

console.log(message);     // #B
```

`#A Outputs: "Hello, world!"`
`#B Uncaught ReferenceError: message is not defined`


Of course IIFE can be written with a fat arrow function:

```typescript
(() => {
  const message = "Hello, World!";
  console.log(message);
})();
```

IIFE is a classic of JavaScript and somewhat dated now post ES2015 where we can create a scope with a block (curly braces) but only for let and const, not for var (which we shouldn't use anyway). This variable is not accessible outside of this block!

```typescript
{
  let blockScopedVariable = "I'm block scoped!";
}
```

Time for a joke. The real reason why the TypeScript function stopped calling the JavaScript function on the phone, is because it didn't want to deal with any more _unexpected arguments_!

In this chapter, we'll meander through the maze of function-related TypeScript snafus, armed with a hearty jest or two and solid, actionable advice. You're in for an enlightening journey! We'll cover the importance of types in functions, rest parameters (not to be confused with resting parameters after a long day), TypeScript utility types, and ah, the infamous this. It's like a chameleon, changing its color based on where it is. It's high time we take a closer look and try to understand its true nature.

So, get comfortable, grab some espresso, and prepare for a few chuckles and plenty of 'Aha!' moments. This chapter promises not only to tickle your funny bone but also to guide you through the maze of TypeScript functions and methods, one laugh at a time.

## 4.1. Omitting Return Types

In TypeScript, it's crucial to have well-defined types throughout your codebase. This includes explicitly defining the return types of functions to ensure consistency and prevent unexpected issues. Omitting return types can lead to confusion, making it difficult for developers to understand the intent of a function or the shape of the data it returns. This section will explore the problems that can arise from omitting return types and provide guidance on how to avoid them.

When you don't specify a return type for a function, TypeScript will try to infer it based on the function's implementation. While TypeScript's type inference capabilities are robust, relying on them too heavily can lead to unintended consequences. For example, if the function's implementation changes, the inferred return type might change as well, which can introduce bugs and inconsistencies in your code.

By explicitly defining return types, you can prevent accidental changes to a function's contract. This makes your code more robust and easier to maintain in the long run, as developers can rely on the return types to understand the expected behavior of a function. Moreover, providing return types in your functions makes your code more self-documenting and easier to understand for both you and other developers who may work on the project. This is particularly important in large codebases and when collaborating with multiple developers.

Also, specifying return types helps ensure consistency across your codebase. This can be particularly useful when working with a team, as it establishes a clear contract for how functions should be used and what they should return. And let's not forget about improved developer experience because with proper function return types, IDEs can offer timely autocompletion and auto suggestions. Although in most cases, IDEs can do this with inferred return types too, that is when we don't explicitly specify the return type but TypeScript tries to guess what the return type is. However, the problem with inferred types (as you'll see later) can sneak in when there's an unintended change of type in the code. Inferred type would change when it shouldn't have been changed (which leads to a bug).

Let's look at examples illustrating the importance of specifying return types. In this first example we have a typical hello world function greet and it doesn't have a return type, meaning TypeScript will infer the type from the code as follows: the return type of the greet function is inferred as string or undefined (i.e., function greet(name: string): string | undefined).

```typescript
function greet(name: string) {
  if (name) {
    return `Hello, ${name}!`;
  }
  return;                           // #A
}

console.log(greet("Afanasiy"));     // #B
console.log(greet(""));             // #C
```

`#A return undefined`
`#B Hello, Afanasiy!`
`#C undefined`

When we pass a truthy string, the function returns a hello string, but when the string is empty (falsy value) or absent (undefined which is also falsy), then the function returns undefined.

You may think that the empty return is superfluous because the function will return anyway. This is true for JavaScript, albeit with TypeScript quite the opposite; without the empty return statement, TypeScript is barking at us "Not all code paths return a value" because it doesn't see a return for the "else" scenario.

So, our silly-simple hello world code works. Nevertheless, by explicitly defining the return type in the second example, you make it clear that the function can return either a string or undefined. This enhances readability, helps prevent regressions, and enforces consistency throughout your codebase.

```typescript
function greet(name: string): string | undefined {
  if (name) {
    return `Hello, ${name}!`;
  }
  return;
}
```

Moreover, having an explicit return type will prevent bugs. For example, if a cat banged its paws on a keyboard to have 42 as the last return, then with inferred types it'll be okay, no errors:

```typescript
function greet(name: string) {
  if (name) {
    return `Hello, ${name}!`;
  }
  return 42;              // #A 
}
```

`#A No errors but it's not correct!`

Conversely, with explicit return type, we would easily catch the bug:

```typescript
function greet(name: string): string | undefined {
  if (name) {
    return `Hello, ${name}!`;
  }
  return 42;              // #A
}
```

`#A Type 'number' is not assignable to type 'string'.`

Next let's look at a more complex example to illustrate the importance of specifying return types in which we have interface, types and functions. In this example, we have type/interface Book, ApiResponse and a function processApiResponse that doesn't have a return type. In the function we calculate a field age "on the fly" and add that to each book (so called virtual field):

```typescript
interface Book {
  id: number;
  title: string;
  author: string;
  publishedYear: number;
}

interface ApiResponse<T> {
  status: number;
  data: T;
}

function processApiResponse(response: ApiResponse<Book[]>) {  // #A
  if (response.status === 200) {
    return response.data.map((book) => ({
      ...book,
      age: new Date().getFullYear() - book.publishedYear,
    }));
  }
  return;
}
```

`#A Without explicit return type, inferred type is { age: number; id: number; title: string; author: string; publishedYear: number; }[] | undefined`

In this example, we have a Book interface and an ApiResponse type that wraps a generic payload. The processApiResponse function takes an ApiResponse containing an array of Book objects and returns an array of processed books with an additional age property, but only if the response status is 200.

We don't specify a return type, and TypeScript infers the return type as ({ id: number; title: string; author: string; publishedYear: number; age: number; }[] | undefined). While this might be correct, it's harder for other developers to understand the intent of the function.

In the following improved version, we create a ProcessedBook type and explicitly define the return type of the function as ProcessedBook[] | undefined to make the function's purpose and return value clearer and easier to understand, improving the overall readability and maintainability of the code:

```typescript
interface ProcessedBook extends Book {        // #A
  age: number;
}

function processApiResponseWithReturnType(
  response: ApiResponse<Book[]>
): ProcessedBook[] | undefined {              // #B
  if (response.status === 200) {
    return response.data.map((book) => ({
      ...book,
      age: new Date().getFullYear() - book.publishedYear,
    }));
  }
  return;
}
```

`#A Throwback to the previous chapter on extending the interfaces`
`#B Explicit function return type`

After that, let me illustrate for you how the processApiResponse and processApiResponseWithReturnType functions are used with sample data to see that both functionally are equivalents:

```typescript
const apiResponse: ApiResponse<Book[]> = {                  // #A
  status: 200,
  data: [
    {
      id: 1,
      title: "The Catcher in the Rye",
      author: "J.D. Salinger",
      publishedYear: 1951,
    },
    {
      id: 2,
      title: "To Kill a Mockingbird",
      author: "Harper Lee",
      publishedYear: 1960,
    },
  ],
};

const processedBooks = processApiResponse(apiResponse);     // #B
console.log(processedBooks);
const processedBooksWithReturnType =
  processApiResponseWithReturnType(apiResponse);            // #C
console.log(processedBooksWithReturnType);
```

`#A Defining the sample data`
`#B Using processApiResponse (without return type)`
`#C Using processApiResponseWithReturnType (with return type)`

Both functions will produce the same output. Nonetheless, by using the processApiResponseWithReturnType function with an explicitly defined return type, you can provide better type safety, improved code readability, and more predictable behavior for anyone who uses the function in the future. To illustrate it, imagine that a developer made incorrect changes to the output of the response function (with the inferred return type). We won't be able to catch mistake:

```typescript
function processApiResponse(response: ApiResponse<Book[]>) {       // #A
  if (response.status === 200) {
    return response.data.map((book) => {
      return {
        id: book.id,
        title: 123,                                                // #B
        age: new Date().getFullYear() - book.publishedYear,
        invalidProp: true,                                         // #C
      };
    });
  }
  return;
}
```

`#A Without explicit return type`
`#B Wrong type number of a correct field title (should be string)`
`#C Wrong new property, while the correct field publishedYear is missing`

The function without return type shown above is prone to have mistakes, because TypeScript cannot catch them. In a function with type, you'll get Type '{ id: number; title: number; age: number; invalidProp: boolean; }[]' is not assignable to type 'ProcessedBook[]'.

Additionally, type inference is not always working properly. For example, we can have a field for primary book cover colors that is an array of either numbers or strings because historically in our database we've been first storing colors in the HEX number format but then switched to HEX string format. Thus, we have some books with an array of strings and other books with an array of numbers. If we have to write a function processColors to process colors, it takes an array of strings or numbers and but wrongly returns an inferred type of array where each value can be a string or a number:

```typescript
function processColors(elements: string[] | number[]) {
  let arr = [];                   // #A
  arr.push(...elements);          // #B
  return arr;                     // #C
}

console.log(
  processColors(                  // #D
    Math.random() > 0.5           // #E
      ? [                         
          `#${Math.floor(Math.random() * 0xffffff).toString(16)}`,
          `#${Math.floor(Math.random() * 0xffffff).toString(16)}`,
        ]
      : [
          Math.floor(Math.random() * 0xffffff),
          Math.floor(Math.random() * 0xffffff),
        ]
  )
);
```

`#A Type is any[]`
`#B Assign items of elements to arr`
`#C Array is incorrectly (string | number)[] but should be string[] | number[]`
`#D Usage of processColors function`
`#E Randomly choose between generating an array of hex color strings or an array of numbers`

To fix this, we can add return type to processColors and define arr as string[] | number[].

In conclusion, _allowing_ TypeScript to infer return types can often make your code shorter and easier to read. It's a TypeScript feature and it's silly not to use it. For instance, it's usually unnecessary to explicitly specify return types for callbacks used with `map` or `filter`. Even more so, excessively detailing return types can make your code more prone to break during refactoring (because there are more places to change code). However, inferred types are not always error prone (as we saw in the example of book colors) and can let bugs sneak in (as we saw with the API response change). When it comes to inferred types for function return types, we should be especially careful.

Therefore, a practical guideline is to explicitly annotate return types for any part of your code that forms an exported API, a contract between components/modules, a public interface and so on. In other words, explicit return type as a safeguard in important places where it's important can improve the overall quality and maintainability of your TypeScript code. As you saw, by being explicit about the expected return values, you can prevent potential issues, enhance readability, and promote consistency across your projects.

## 4.2. Mishandling Types in Functions

Function types in TypeScript enable you to define the expected input and output types for callback functions, providing type safety and making your code more robust. Not using function types for callbacks can lead to confusion, hard-to-find bugs, and less maintainable code. This section discusses the importance of using function types for callbacks and provides examples of how to do so correctly.

### 4.2.1. Not Specifying Callback Function Types

When defining functions that accept callbacks, it is important to specify the expected callback function types, as this helps to enforce type safety and prevents potential issues.

Let's take a look at a bad example in which it's easy to make a mistake because TypeScript won't error. Here we are using a callback function with more parameters than provided:

```typescript
function processData(data: string, callback: Function): void {
  // Process data...
  callback(data);
}

processData("a", (b: string, c: string) => console.log(b + c)); // #A
```

`#A No TypeScript error and the output is "aundefined"`

In the example above, TypeScript won't be able to catch any errors related to the callback function because it's defined as a generic Function. A good example in which TypeScript will alert us about mismatched types of callback functions needs to have the callback function type defined properly:

```typescript
type DataCallback = (data: string) => void;           // #A

function processData(data: string, callback: DataCallback): void {
  // Process data...
  callback(data);
}

processData("a", (b: string) => console.log(b));      // #B
processData("a", (b: string, c: string) => console.log(b + c)); // #C
```

`#A Properly defined callback function type`
`#B Ok: no errors and the usage as it should be`
`#C Wrong usage and hence we get Error: Argument of type '(b: number, c: number) => void' is not assignable to parameter of type 'DataCallback'`

In the good example, we define a DataCallback function type that specifies the expected input and output types for the callback function, ensuring type safety. Of course, we can define the callback function type inline without the extra type DataCallback, like this:

### 4.2.2. Inconsistent Parameter Types

When defining function types for callbacks, it's crucial to ensure that the parameter types are consistent across your application. This helps to avoid confusion and potential runtime errors.

Here's an example in which we have two classes for callbacks for the apiCall function. But in the actual apiCall instead of using both two types we only use one. This leaves the function parameter success inconsistent with the defined type (that can be used elsewhere in the code), which in turn can lead to errors. So, here's the bad example:

```typescript
type SuccessCallback = (result: string) => void;
type FailureCallback = (error: string) => void;
function apiCall(success: (data: any) => void, failure: FailureCallback) {
  // Implementation...
}
```

As you can see SuccessCallback represents a function that takes one parameter of type string and does not return anything (void). On the other hand, the first parameter, success, is a function that takes one parameter of type any and does not return anything. It's intended to be a callback function that gets called when the API call is successful. Let's fix this in a good example:

```typescript
type SuccessCallback = (result: string) => void;
type FailureCallback = (error: string) => void;
function apiCall(success: SuccessCallback, failure: FailureCallback) {
  // Implementation...
}
```

By consistently using the defined function types for callbacks, you can ensure that your code is more maintainable and less prone to errors.

### 4.2.3. Lacking Clarity with Callbacks

When you don't use function types for callbacks, the expected inputs and outputs might not be clear, leading to confusion and potential errors.

Here's a suboptimal example that defines a function named processData that takes two arguments. The first argument, data, is expected to be a string. This could be any kind of data that needs processing, perhaps a file content, an API response, or any data that's represented as a string. The second argument, callback, is a function. This is a common pattern in Node.js and JavaScript for handling asynchronous operations. In this case, the callback function itself accepts two arguments:

- error: which is either an Error object (if an error occurred during the processing of the data) or null (if no errors occurred).

- result: which is either a string (representing the processed data) or null (if there is no result to return, perhaps due to an error).

Inside the processData function, we are "processing" the data argument by converting it to uppercase (and maybe doing something more), and once that's completed, we would call the callback function, passing it the error (or null if there's no error), and the result (or null if there's no result):

```typescript
function processData(
  data: string,
  callback: (                               // #A
  error: Error | null,
  result: string | null
) => void) {
  let processedData = null;
  try {                                     // #B
    processedData = data.toUpperCase();     // #C
    callback(null, processedData);
  } catch (error) {
    callback(error, null);
  }
}
```

`#A Defining type inline for the callback`
`#B Hypothetical processing operation`
`#C Convert the data to uppercase`

Let's say this callback is encountered in many other places in the code. Thus, a more optimal example would include a new type alias ProcessDataCallback to improve code reuse:

```typescript
type ProcessDataCallback = (                // #A
  error: Error | null,
  result: string | null
) => void;

function processData(                       // #B
  data: string,
  callback: ProcessDataCallback
) {
  // ...Process data and invoke the callback
}
```

`#A Defining type alias for the callback`
`#B Function using the type alias`

Of course, as we've seen in previous chapters, for this case an interface instead of the type alias is feasible too. To convert the given function and type into an interface, you would define an interface for the callback and then use that interface within the function signature. Here's how it could look:

```typescript
interface ProcessDataCallback {             // #A
  (error: Error | null, result: string | null): void;
}

function processData(                       // #B
  data: string, 
  callback: ProcessDataCallback) { 
  // ...Process data and invoke the callback
}
```

`#A Defining the interface for the callback`
`#B Function using the interface`

This structure allows for the same functionality and type safety as the original version with a type alias but uses an interface, which might be preferred in certain coding styles or for extending types in more complex scenarios.

To sum up, using function types for callbacks in TypeScript is crucial for providing type safety, consistency, and maintainability in your codebase. Lean on the side of defining appropriate function types (inline or as a separate type alias) for your callbacks to prevent potential issues and create more robust applications. By using a function type (inline, alias or interface) for the callback, we make the code more explicit and easier to understand.

## 4.3. Misusing Optional Function Parameters

Optional parameters in TypeScript are a powerful feature that allows you to create more flexible and concise functions. However, they can sometimes be misused, leading to potential issues and unexpected behavior. In this section, we'll explore common mistakes developers make when using optional parameters in TypeScript and how to avoid them.

Let's start with a mistake of using optional parameters when default parameters would be more appropriate. Default parameters are parameters where we set the value if no value is provided. Optional parameters can lead to unnecessary conditional logic inside the function to handle the case when the parameter is not provided, while default parameters set the value in a concise manner, right in the function signature. The following code is code with optional parameters but without the default parameter and, as you can see, it requires an extra "if/else" like logic to properly handle timeout:

```typescript
function fetchData(url: string, timeout?: number) {
  const actualTimeout = timeout ?? 3000;             // #A
}
```

`#A Default to 3000ms if timeout is not provided and fetch data with the actualTimeout`

We use an optional parameter for timeout and default to 3000 if it's not provided. Next let's see how default parameter can transform our small example. Instead, we can use a default parameter to achieve the same effect more concisely (an added perk is that we can drop the :number type annotation since TypeScript infers it):

```typescript
function fetchData(url: string, timeout = 3000) {    // #A
  // ...
}
```

`#A Fetch data with the default timeout parameter`

After that, we can dive deeper into relying on implicit undefined values. When using optional parameters, it's essential to understand that, by default, they are implicitly assigned the value undefined when not provided. This can lead to unintended behavior if your code doesn't have explicit checks. To avoid this issue, handle undefined values explicitly or provide default values for optional parameters (as shown previously).

Consider the following case of a function createPerson object, where undefined values of lastName and age can cause problems:

```typescript
function createPerson(firstName: string, lastName?: string, age?: number) {
  const person = {
    fullName: `${firstName} ${lastName}`,     // #A
    isAdult: age > 18,                        // #B
  };
  return person;
}

const person1 = createPerson("Pavel", "Ivanov", 30);
const person2 = createPerson("Kim", undefined, 16);
const person3 = createPerson("Satish", "");

console.log(person1);                         // #C
console.log(person2);                         // #D
console.log(person3);                         // #E
```

`#A No TypeScript warning/error when trying to use optional parameter that's possibly undefined`
`#B Problematic usage of implicit undefined value with TypeScript error: 'age' is possibly 'undefined'`
`#C Correct { fullName: 'Pavel Ivanov', isAdult: true }`
`#D Incorrect last name { fullName: 'Kim undefined', isAdult: false }`
`#E We don't know if Satish is an adult or not { fullName: 'Satish ', isAdult: false }`

In this example, the problematic usage of the implicit undefined value is when checking if the age is greater than 18. Since undefined is falsy, the comparison undefined > 18 evaluates to false. While this might work in this particular case, it could potentially introduce bugs in more complex scenarios. And yes, we do have a TypeScript error warning age is possibly undefined; and you even might say because of the warning, the error is very easy to notice and fix, that it's not an issue. Why waste paper on this topic? And I agree with you, but because the code can still compile and run, the error can be spotted and fixed _only_ if a developer doesn't have other errors that can hide this particular error, _and_ if this developer is disciplined about fixing all the errors (as we all should be). Hence, it's worth highlighting the feasible source of bugs.

A better approach would be to explicitly check for undefined to handle it appropriately (N/A) or provide a default value for age and thus the default value for isAdult. This is an example with a ternary expression age !== undefined:

```typescript
function createPerson(firstName: string, lastName?: string, age?: number) {
  const person = {
    fullName: lastName ? `${firstName} ${lastName}` : firstName,
    isAdult: age !== undefined ? age > 18 : "N/A", // #A
  };
  return person;
}

const person1 = createPerson("Pavel", "Ivanov", 30);
const person2 = createPerson("Kim", undefined, 16);
const person3 = createPerson("Satish");

console.log(person1); // #B
console.log(person2); // #C
console.log(person3); // #D
```

`#A Added an undefined check`
`#B Correct: { fullName: 'Pavel Ivanov', isAdult: false }`
`#C Correct: { fullName: 'Kim', isAdult: false }`
`#D Correct: { fullName: 'Satish', isAdult: 'N/A' }`

Note, that if we just use a truthy check isAdult: (age) ? age > 18 : 'N/A', then all the babies aged younger than 1 years of old (age is 0), will be incorrectly assumed as undetermined (N/A) when in fact they should be isAdult: false. This is because a truthy check considers values 0, NaN, falsy and an empty string as falsy when in fact they can be valid values (like our age of 0 for babies).

Lastly, placing required parameters _after_ optional ones is kind of a mistake related to optional parameters. Albeit it is that a very sneaking mistake (i.e., hard to not notice), because TypeScript will warn us if we attempt to write something like this:

```typescript
function fetchData(
  url: string,
  timeout?: number,
  callback: () => void // #A
) { 
  // Fetch data and call the callback
}
```

`#A Error: A required parameter cannot follow an optional parameter.`

In summary, optional parameters are a powerful feature in TypeScript, but it's crucial to use them correctly to avoid potential issues and confusion. By following best practices such as ordering parameters, using default parameters when appropriate, and handling undefined values explicitly, you can create more flexible and reliable functions in your TypeScript code.

## 4.4. Inadequate Use of Rest Parameters

We continue focusing on functions and their signatures with rest parameters. They are a convenient feature in TypeScript (and JavaScript) that allows you to capture an indefinite number of arguments as an array. However, improper usage of rest parameters can lead to confusion and potential issues in your code. In this section, we will discuss some common mistakes when using rest parameters and how to avoid them.

### 4.4.1. Using Rest Parameters with Optional Parameters

The first rest mistake is using rest parameters in conjunction with optional parameters. This combination can be confusing and may lead to unexpected behavior. It is better to avoid using rest parameters with optional parameters and find alternative solutions.

Confusing when optional parameter is forgotten but rest parameters are passed:

```typescript
function sendMessage(to: string, cc?: string, ...attachments: string[]) {
  console.log("to:", to, "cc: ", cc, "attachments: ", ...attachments);
}

sendMessage("a@qq.com");
sendMessage("a@qq.com", "b@qq.com");
sendMessage("a@qq.com", "attachment1", "attachment2");            // #A
sendMessage("a@qq.com", undefined, "attachment1", "attachment2"); // #B
```

`#A cc becomes "attachment1", and attachments has only ["attachment2"]`
`#B this is how it really should be called`

A better approach is to use an object parameter to a function (arguments) instead of multiple parameters. This is helpful with complex cases such as having multiple optional parameters including rest. In the parameters type, we can specify optional parameters (properties of the type):

```typescript
function sendMessage(params: {
  to: string;
  cc?: string;
  attachments?: string[];
}) {
  console.log(params);
}

sendMessage({         // #A
  to: "a@qq.com",    
}); 

sendMessage({         // #B
  to: "a@qq.com",     
  cc: "b@qq.com",
}); 

sendMessage({         // #C
  to: "a@qq.com",
  attachments: ["attachment1", "attachment2"],
}); 

sendMessage({         // #D
  to: "a",
  cc: "b",
  attachments: ["attachment1", "attachment2"],
}); 
```

`#A Just "to" is okay`
`#B "to" and "cc" is okay too`
`#C "to" without "cc" but with "attachments" is fine`
`#D Everything provided is fine too`

### 4.4.2. Using Correct Types

Rest parameters can sometimes be confused with array parameters, which can lead to unexpected behavior. While rest parameters collect individual arguments into an array, array parameters accept an array as an argument. Make sure to use the correct parameter type based on your requirements.

The correct way to define the rest parameter is to use an array, i.e., to use a single square brackets following the type, e.g., string[]. This way the rest parameter, e.g., messages is an array of strings while the parameters are passed one by one with commas:

```typescript
function logMessages(...messages: string[]) {
  // ...
}

logMessages("1", "2", "3", "a", "b", "c");      // #A
```

`#A Ok: Proper rest parameter usage with passing multiple parameters of type string`

We can also have a mix of types for parameters, as follows:

```typescript
function logMessages(...messages: (string |number)[]) {
  console.log(messages)

}

logMessages(1,2,3, 'a', 'b', 'c')
```

The following example shows an *incorrect* usage and type array of arrays of strings (string[][]):

```typescript
function logMessages(...messages: string[][]) {
  console.log(messages)
}

logMessages('1','2','3', 'a', 'b', 'c')         // #A
```

`#A Error: Argument of type 'string' is not assignable to parameter of type 'string[]'.`

In the beginning of this section, I've said that rest parameters are a convenient JavaScript/TypeScript feature. However nowadays, while I still see it in some libraries that need (want?) to support variable number of parameters, I rarely see it being used in the application code. Example of such libraries are lodash, D3 and others. Instead in the application code, it's more popular to use an array parameter, e.g.,

```typescript
function logMessages(messages: string[]) {
  // ...
}

logMessages(["1", "2", "3", "a", "b", "c"]);    // #A
```

`#A Invoked properly as single array of many string items`

I tend to prefer array parameter over rest parameter too, because this approach is more robust (as we have seen in the mistake with optional parameters). Which leads us to the next topic.

### 4.4.3. Unnecessarily Complicating the Function Signature

One mistake when using rest parameters is making the function signature more complicated than it needs to be. For example, consider the following function that takes an arbitrary number of strings and concatenates them:

```typescript
function concatenateStrings(...strings: string[]): string {
  return strings.join("");
}
```

While this function works correctly, it might be more straightforward to accept an array of strings instead of using a rest parameter. By accepting an array, the function signature becomes more concise and easier to understand:

```typescript
function concatenateStrings(strings: string[]): string {
  return strings.join("");
}
```

While rest parameters can be useful, overusing them can lead to overly flexible functions that are difficult to understand and maintain. Functions with a large number of rest parameters can be challenging to reason about and may require additional documentation or comments to explain their behavior.

### 4.4.4. Overusing Rest Parameters

Another mistake is to overuse rest parameters, especially when the function only expects a limited number of arguments. This can make it difficult to understand the function's purpose and increase the likelihood of errors.

```typescript
function createProduct(
  name: string,
  price: number,
  ...attributes: string[]
) {
  // Function implementation
}
```

In this example, the createProduct function uses a rest parameter for product attributes. However, if the function only expects a few specific attributes, it would be better to use individual parameters or an object for those attributes:

```typescript
function createProduct(
  name: string,
  price: number,
  color: string,
  size: string
) {
  // Function implementation
}
```

Or, with a nested property attributes:

```typescript
function createProduct(
  name: string,
  price: number,
  attributes: {
    color: string;
    size: string;
  }
) {
  // Function implementation
}
```

In general, it's best to use rest parameters sparingly and only when they significantly improve the clarity or flexibility of your code. By being mindful of these common mistakes and following best practices when using rest parameters, you can create more flexible and clear functions in your TypeScript code.

## 4.5. Not Understanding this

Let's start with a joke. Occasionally while coding in JavaScript, I feel the urge to give up and exclaim, "this is ridiculous!" but I always forget what "this" actually denotes. :-)

Indeed, this in TypeScript, as in JavaScript, refers to the context of the current scope. It's used inside a method to refer to the object that the function is a method of. When you call a method on an object, the object is passed into the method as this. However, this can sometimes behave in unpredictable ways in JavaScript, especially when functions are passed as arguments or used as event handlers. TypeScript helps manage these difficulties by allowing you to specify the type of this in function signatures.

These are examples on how you can use this properly in TypeScript.

You can use this inside a class to refer to the class:

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;                                 // #A
  }

  sayHello() {
    console.log(`Hello, my name is ${this.name}`);    // #B
  }
}

const person = new Person("Irina");
person.sayHello();                                    // #C
```

`#A 'this' refers to the instance of the class`
`#B 'this' refers to the instance of the class`
`#C Outputs: "Hello, my name is Irina"`

In Person, we defined the property name with a string type. Then we set the value of name using this.name in constructor (initializer), so that during the instantiation of Person property name would be set to the value passed to new Person(). The same approach can be used in other methods, not just constructors.

In JavaScript/TypeScript, you can use this in (fat) arrow functions. (The term fat arrow function comes from CoffeeScript which I liked, and where we also had a _thin_ arrow function -> that is sadly not present in JavaScript/TypeScript.) Arrow functions don't have their own this context, so this inside an arrow function refers to the this from the surrounding scope. In other words, the arrow function "locks" this to the context as it's written in code, not as it's executed. At least this is how I remember it. This can be useful for event handlers and other callback-based code that are "divorced" from the context in which they are written due to the way the JavaScript event loop or a browser DOM are executing them. For example, we all know the setTimeout function and that the event loop will invoke it later. This code snippet tries to access (successfully) an object property (this.name) from within the setTimeout:

```typescript
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  waitAndSayHello() {
    setTimeout(() => {                                // #A
      console.log(`Hello, my name is ${this.name}`);  // #B
    }, 1000);
  }
}

const person = new Person("Elena");
person.waitAndSayHello();                             // #C
```

`#A Arrow function to "keep" the value of this`
`#B 'this' refers to the instance of the class, not the setTimeout function`
`#C Outputs: "Hello, my name is Elena" after 1 second`

In this example, if we used a regular function for the setTimeout callback, this.name would be undefined, because this inside setTimeout refers to the global scope (or is undefined in strict mode). However, because we used an arrow function, this still refers to the instance of the Person class.

In TypeScript, but not JavaScript, you can use this in function signature. In fact, it's considered _the_ best practice is to specify this type in a function signature, if the function uses this (sets or accesses this). For example, we have the greet function that requires this to be of a certain shape:

```typescript
function greet(this: { name: string }) {
  console.log(`Hello, my name is ${this.name}`);
}

const person = {
  name: "Nikolai",
  greet,        // #A
};

person.greet(); // #B
```

`#A Compact object notation for greet: greet`
`#B Outputs: "Hello, my name is Nikolai"`

In this example, we've specified that this should be an object with a name property. If we try to call greet() on an object without a name, TypeScript will throw an error.

Last but not least, you can leverage this in TypeScript interfaces to reference the current type. Consider the following example that implements a method chaining for the option method.

```typescript
interface Chainable {
  option(key: string, value: any): this;
}

class Config implements Chainable {
  options: Record<string, any> = {};
  option(key: string, value: any): this {
    this.options[key] = value;
    return this;
  }
}

const config = new Config();
config.option("user", "Ivan").option("role", "admin");    // #A
```

`#A method chaining works because 'option' returns 'this'`

In this example, option in the Chainable interface is defined to return this, which means it returns the current instance of the class. This allows for method chaining, where you can call one method after another on the same object. We can also have a function return type as Config instead of this, but the actual return in the option method has to be this and nothing else.

It's worth noting that this supports polymorphism behavior in classes. When a method in a base class refers to "this", and that method is called from an instance of a subclass, "this" refers to the instance (object) of the subclass. This ensures that the correct methods or properties are accessed, even if they are overridden or extended in the subclass, allowing for dynamic dispatch of method calls. Also, In TypeScript, when subclassing, you need to call the constructor of the base class using super(). Inside the constructor of the subclass, "this" can't be used before calling super(), because the base class's constructor must execute first to ensure the object is properly initialized. After the super() call, this refers to the new subclass instance, fully initialized with the base class properties, and can be used to further modify or set up the subclass instance.

```typescript
class SubConfig extends Config {
  appName: string;

  constructor(appName: string) {
    super();
    this.appName = appName;
  }
}

const mcFlurry = new SubConfig("McFlurry");
mcFlurry.option("user", "Ivan");
```

Always be aware of the context in which you're using this. If a method that uses this is called in a different context (like being passed as a callback), this might not be what you expect. To mitigate this, you can bind the method to this:

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
    this.sayHello = this.sayHello.bind(this);     // #A
  }

  sayHello() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

let sayHelloFn = new Person("Ivan").sayHello;
sayHelloFn();                                     // #B
```

`#A bind sayHello to the instance of the class, without it error: undefined is not an object`
`#B Outputs: "Hello, my name is Ivan"`

In this preceding example, even though sayHello is called in the global context, it still correctly refers to the instance of the Person class because we bound this in the constructor.

Remember that this binding is not necessary when using arrow functions within class properties, as arrow functions do not create their own this context:

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  sayHello = () => {    // #A
    console.log(`Hello, my name is ${this.name}`);
  };
}

let sayHelloFn = new Person("Ivan").sayHello;
sayHelloFn();           // #B
```

`#A Using an arrow function`
`#B Outputs: "Hello, my name is Ivan"`

In the aforementioned example, sayHello is an arrow function, so it uses the this from the Person instance, not from where it's called. This is _the_ preferred approach to explicit this binding in the constructor. I'm a big fan of this technique!

More so, there's also the global this but it deserves its own section and I'll cover it later.

### 4.5.1. Misleveraging ThisParameterType for better types safety of the this context

In this section, we will explore an advanced TypeScript feature called ThisParameterType that provides enhanced type safety when dealing with the this context within functions or methods. TypeScript provides a built-in utility type called ThisParameterType that allows us to extract the type of the this parameter in a function or method signature.

When working with functions or methods that rely on proper this context, it is crucial to ensure type safety to prevent potential runtime errors. By utilizing ThisParameterType, we can enforce correct this context usage during development, catching any potential issues before they occur.

Thus, the ThisParameterType utility type in TypeScript enables us to extract the type of the this parameter in a function or method signature. By using ThisParameterType, we can explicitly specify the expected this context type, providing improved type safety and preventing potential runtime errors. When defining functions or methods that rely on a specific this context, consider using ThisParameterType to ensure accurate typing and enforce correct usage.

Here's a suboptimal example in which this is used in the function introduce implicitly and this has type any and it does _not_ have a type annotation. We also use object literal to create an object person with this function, which in a sense becomes a method person.introduce. Thus, providing necessary parameters name and age to the method:

```typescript
function introduce(): void {  // #A
  console.log(`Hi, my name is ${this.name} and I am ${this.age} years old.`); // #A
}

const person = {
  name: "Arjun",
  age: 30,
  introduce,
};

person.introduce();           // #B
```

`#A Function outside of our code that we cannot modify`
`#B Error: 'this' implicitly has type 'any' because it does not have a type annotation.`

The above code is suboptimal because we have this as any and because if someone tries to (incorrectly) call the method with a different context, we won't see any problem with it until it's too late. For example, this statement that doesn't pass the proper name nor age will cause run-time error but not the TypeScript error:

```typescript
person.introduce.call({});
```

A more optimal example would have type annotation for this and an interface Person for added type safety:

```typescript
function introduce(this: { name: string; age: number }): void {
  console.log(`Hi, my name is ${this.name} and I am ${this.age} years old.`);
}

interface Person {
  name: string;
  age: number;

  introduce(this: { 
    name: string; 
    age: number 
  }): void;
}

const person: Person = {
  name: 'Arjun',
  age: 30,
  introduce,
};

person.introduce();                     // #A
person.introduce.call({});              // #B
```

`#A Hi, my name is Arjun and I am 30 years old.`
`#B Error: Argument of type '{}' is not assignable to parameter of type '{ name: string; age: number; }'.`

But now let's remember that we also have a utility called ThisParameterType. It allows us to extract this. Ergo, the most optimal (and type-safest) example would use ThisParameterType to avoid repeating type definitions of name and age in type Person (note the use of &):

```typescript
function introduce(this: { name: string; age: number }): void {
  console.log(`Hi, my name is ${this.name} and I am ${this.age} years old.`);
}

type introduceType = typeof introduce;
type introduceContext = ThisParameterType<introduceType>;

type Person = {     // #A

  introduce(this: introduceContext): void;
  email: string;    // #B
} & introduceContext;

const person: Person = {
  name: "Arjun",
  age: 30,
  email: "arjun@hotmail.com",
  introduce,
};

person.introduce();         // #C
person.introduce.call({});  // #D
```

`#A Type that has properties of this from introduce and introduce method`
`#B We can add extra properties to Person type, it doesn't have to be exactly as introduce context`
`#C Hi, my name is Arjun and I am 30 years old.`
`#D Argument of type '{}' is not assignable to parameter of type '{ name: string; age: number; }'.`

Or for brevity (but less readability), we can combine type like this:

```typescript
type Person = {
  introduce(this: ThisParameterType<typeof introduce>): void;
} & ThisParameterType<typeof introduce>;
```

### 4.5.2. Not removing this with OmitThisParameter

OmitThisParameter is a utility type in TypeScript that removes the this parameter from a function's type, if it exists. This is useful when you're dealing with a function that has a this parameter, but you want to pass it to some code that doesn't expect a this parameter.

For instance, consider a function type that includes a this parameter:

```typescript
type MyFunctionType = (this: string, foo: number, bar: number) => void;
```

If you try to use this function in a context where a this parameter is not expected, you'll get a type error:

```typescript
function callFunction(fn: (foo: number, bar: number) => void) {
  fn(1, 2);
}

let myFunction: MyFunctionType = function (foo: number, bar: number) {
  console.log(this, foo, bar);
};

callFunction(myFunction);   // #A
```

`#A Error: Types of parameters '__0' and 'foo' are incompatible`

Here, callFunction expects a function that takes two number parameters, but myFunction includes a this parameter, so it's not compatible.

You can use OmitThisParameter to remove the this parameter:

```typescript
function callFunction(fn: OmitThisParameter<MyFunctionType>) {
  fn(1, 2);
}

let myFunction: MyFunctionType = function (foo: number, bar: number) {
  console.log(this, foo, bar);
};

callFunction(myFunction);   // #A
```

`#A No error`

Here, OmitThisParameter<MyFunctionType> is a type that is equivalent to (foo: number, bar: number) => void. This means you can pass myFunction to callFunction without any type errors.

Note that OmitThisParameter doesn't actually change the behavior of myFunction. When myFunction is called, this will be undefined, because callFunction calls fn without specifying a this value. If myFunction relies on this being a string, you'll need to ensure that it's called with the correct this value.

In conclusion, using this in TypeScript involves understanding its behavior in JavaScript and making use of TypeScript's features to avoid common mistakes. By declaring the type of this, you can avoid many common errors and make your code more robust and easier to understand. And if you can avoid using this, maybe you should because there are still a lot of developers out there for whom it is still inherently confusing. (Instead of this, we can rewrite class-base code to use more function-style code with plain functions, closures, or data passed explicitly through function parameters.)

### 4.6. Being unaware of call, bind, apply and strictBindCallApply

bind, call, and apply can be very useful when working with this context. Here's an example that shows all three. We create a Person class that has greet and greetWithMood functions. These two functions use this. By leveraging bind, call, and apply we can "change" the value of this.

```typescript
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;   // #A
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }

  greetWithMood(mood: string) {
    console.log(
      `Hello, my name is ${this.name}, and I'm currently feeling ${mood}`
    );
  }
}

let tim = new Person("Tim");
let alex = new Person("Alex");

tim.greet.call(alex);                       // #B
tim.greetWithMood.apply(alex, ["happy"]);   // #C

let boundGreet = tim.greet.bind(alex);      // #D
boundGreet();                               // #E
```

`#A 'this' refers to the instance of the class`
`#B Use call: calls greet with 'this' set to alex; Outputs: "Hello, my name is Alex"`
`#C Use apply: calls greetWithMood with 'this' set to alex and arguments as an array; Outputs: "Hello, my name is Alex, and I'm currently feeling happy"`
`#D Use bind: creates a new function with 'this' set to alex`
`#E Outputs: "Hello, my name is Alex"`

In this example:

- call is a method that calls a function with a given this value and arguments provided individually.

- apply is similar to call, but it takes an array-like object of arguments.

- bind creates a new function that, when called, has its this keyword set to the provided value.

As before, the key idea is that we're able to call methods that belong to one instance of Person (Tim) and change their context to another instance of Person (Alex).

TypeScript 3.2 introduced a strictBindCallApply compiler option that provides stricter checking for bind, call, and apply:

```typescript
function foo(a: number, b: string): string {
  return a + b;
}

let a = foo.apply(undefined, [10]);         // #A

let b = foo.call(undefined, 10, 2);         // #B

let c = foo.bind(undefined, 10, "hello")(); // #C
```

`#A Error: Expected 2 arguments, but got 1`
`#B Error: Argument of type '2' is not assignable to parameter of type 'string'`
`#C OK`

In this example, TypeScript checks that the arguments passed to apply, call, and bind match the parameters of the original function.

## 4.7. Not Knowing About globalThis

Getting to the global object in JavaScript has been kind of a mess historically. If you're on the web, you could use window, self, or frames - but if you're working with Web Workers, only self flies. And in Node.js? None of these will work. You gotta use global instead. You could use the this keyword inside non-strict functions, but it's a no-go in modules and strict functions.

Let's see these in a few examples. In TypeScript (and JavaScript), the this keyword behaves differently depending on the context in which it's used. In the global scope, this refers to the global object. In browsers, the global object is window, so in a browser context, this at the global level will refer to the window object:

```typescript
console.log(this === window);   // #A
```

`#A logs 'true' in a browser context`

In Node.js, the situation is a bit different. Node.js follows the CommonJS module system, and each file in Node.js is its own module. This means that the top-level this does not refer to the global object (which is global in Node.js), but instead it refers to exports of the current module, which is an empty object by default. So, in Node.js:

```typescript
console.log(this === global);   // #A
console.log(this);              // #B
```

`#A logs 'false'`
`#B logs '{}' or { exports: {} }`

However, inside functions that are not part of any object, this defaults to the global object, unless the function is in strict mode, in which case this will be undefined. Here's an example:

```typescript
function logThis() {
  console.log(this);
}

logThis();                      // #A

function strictLogThis() {
  "use strict";
  console.log(this);
}

strictLogThis();                // #B
```

`#A logs 'global' in Node.js, 'window' in browser (if not in strict mode)`
`#B logs 'undefined'`

In TypeScript, you can use this in the global scope, but it's generally better to avoid it if possible, because it can lead to confusing code. It's usually better to use specific global variables, like window or global, or to avoid global state altogether. The behavior of this is one of the more complex parts of JavaScript and TypeScript, and understanding it can help avoid many common bugs.

Enter globalThis. It's a pretty reliable way to get the global this value (and thus the global object itself) no matter where you are. Unlike window and self, it's working fine whether you're in a window context or not (like Node). So, you can get to the global object without stressing about the environment your code's in. Easy way to remember the name? Just think "in the global scope, this is globalThis". Boom.

So, In JavaScript, globalThis is a global property that provides a standard way to access the global scope (the "global object") across different environments, including the browser, Node.js, and Web Workers. This makes it easier to write portable JavaScript code that can run in different environments. In TypeScript, you can use globalThis in the same way. However, because globalThis is read-only, you can't directly overwrite it. What you can do is add new properties to globalThis.

For instance, if you add a new property to globalThis, you'll get Element implicitly has an 'any' type because type 'typeof globalThis' has no index signature:

```typescript
globalThis.myGlobalProperty = "Hello, world!";
console.log(myGlobalProperty);    // #A
```

`#A Logs: "Hello, world!"`

If you try window.myGlobalProperty, then you'll get `Property 'myGlobalProperty' does not exist on type 'Window & typeof globalThis'.What we need to do is to declare type:

```typescript
// typings/globals.d.ts (depending on your tsconfig.json)

export {};           // #A

interface Person {   // #B
  name: string;
}

declare global {
  var myGlobalProperty: string;
  var globalPerson: Person;
}
```

`#A We need this to make the file into a module`
`#B instances of a class can be global properties too`

The above code adds the following types:

```typescript
myGlobalProperty;
window.myGlobalProperty;
globalThis.myGlobalProperty;
globalPerson.name;
window.globalPerson.name;
globalThis.globalPerson.name;
```

In this example, declare global extends the global scope with a new variable myGlobalProperty. After this declaration, you can add myGlobalProperty to globalThis without any type errors.

Remember that modifying the global scope can lead to confusing code and is generally considered bad practice. It can cause conflicts with other scripts and libraries and makes code harder to test and debug. It's usually better to use modules and local scope instead. However, if you have a legitimate use case for modifying the global scope, TypeScript provides the tools to do it in a type-safe way.

Another common use of globalThis in TypeScript and JavaScript is to check for the existence of global variables. For example, in a browser environment, you might want to check if fetch is available:

```typescript
if (!globalThis.fetch) {
  console.log("fetch is not available");
} else {
  fetch("https://example.com")
    .then((response) => response.json())
    .then((data) => console.log(data));
}
```

In this example, globalThis.fetch refers to the fetch function, which is a global variable in modern browsers. If fetch is not available, the code logs a message to the console. If fetch is available, the code makes a fetch request.

This can be useful for feature detection, where you check if certain APIs are available before you use them. This helps ensure that your code can run in different environments.

Remember, it's better to avoid modifying the global scope if you can, and to use globalThis responsibly. Modifying the global scope can lead to conflicts with other scripts and libraries and makes your code harder to test and debug. It's usually better to use modules and local scope instead. In modern JavaScript and TypeScript development, modules provide a better and more flexible way to share code between different parts of your application.

## 4.8. Disregarding Function Signatures in Object Type

We can define a function signature in the object type. This means that the object can be called as a function and specifies the types of parameters and the return type of that function. Essentially, a call signature defines how a function can be called and what it returns, directly within the structure of an object type. A call signature is written similarly to a function declaration, but without the function name. It consists of a set of parentheses around the parameter list, followed by a colon and the return type. Here's the general structure (can also be an interface):

```typescript
type FunctionSignatureObjectType = {
  // ... Some properties
  (param1: Type1, param2: Type2, ...): ReturnType;        // #A
};
```

`#A Function signature`

To understand this better, let's look at an example of the function signature in an object type Greeter:

```typescript
type Greeter = {
  (name: string): string;           // #A
};

const sayHello: Greeter = (name) => `Hello, ${name}!`;          // #B
console.log(sayHello("Alisa"));     // #C
```

`#A This is the call signature within the object type`
`#B A function that meets the type definition of Greeter`
`#C Using the function; output: Hello, Alisa!`

In this example, Greeter is an object type with a call signature. It specifies that any object of type Greeter is actually a function that takes a single string parameter and returns a string. The sayHello function is then defined to match this call signature. It takes a string _name_ and returns a greeting message, also as a string.

Moreover, in TypeScript you can define an object type with both properties and call signatures. This means the object can have regular properties (like numbers, strings, etc.) and also be callable as a function. Here's an example of how you might define and use such an object (using type alias or interface):

```typescript
interface UserCreator {
  defaultId: string;                // #A
  defaultName: string;
  (name: string, id: string): User; // #B
}

interface User {
  name: string;
  id: string;
}
```

`#A Properties`
`#B Call signature`

In this example, UserCreator is an object type with two properties, defaultId and defaultName, and a call signature that creates a user when called with a name and an id. Next, let's implement a specific instance of UserCreator:

```typescript
const createUser: UserCreator = function (
  this: UserCreator,                // #A
  name: string,
  id: string
): User {
  return {
    name: name || this.defaultName, // #B
    id: id || this.defaultId,
  };
};

createUser.defaultId = "0000";      // #C
createUser.defaultName = "NewUser";

const user1 = createUser("Alisa", "1234"); // #D
console.log(user1);                // #E

const user2 = createUser("", "");  // #F
console.log(user2);                // #G
```

`#A Defining the type of "this"`
`#B Using this to access class properties`
`#C Assigning properties to the function object`
`#D Using the object`
`#E {name: "Alisa", id: "1234"}`
`#F Uses default values`
`#G {name: "NewUser", id: "0000"}`

In this example, the UserCreator type is defined as an object that functions both as a creator for a User and as a holder for default user properties. The createUser function, instance of the UserCreator type, requires two parameters to return a User, and internally, it utilizes the defaultId and defaultName from its own context, known as this, which refers to the function object. Before these default properties can be utilized, they must be specifically assigned to the createUser object. When createUser is called, much like any standard function, it generates new User objects. Notably, if provided with empty strings, createUser will instead apply the default values that have been set to its properties. This works because in JavaScript all functions are objects, hence we are able to have properties on the function object createUser.

To sum it up, using function signatures in object types along with additional properties allows you to create rich, stateful functional objects that encapsulate both behavior and data in a structured way. This can be especially useful in scenarios like factory functions, configurable functions, or when mimicking classes while leveraging function flexibility.

## 4.9. Incorrect Function Overloads

Function overloads in TypeScript allow you to define multiple function signatures for a single implementation, enabling better type safety and more precise type checking. In order to achieve this, create multiple function signatures (typically two or more) and follow them with the implementation of the function.

However, incorrect use of function overloads can lead to confusion, subtle bugs, and increased code complexity. In this section, we will discuss common mistakes when using function overloads and provide guidance on how to use them correctly.

### 4.9.1 Using mismatched overload signatures

When creating function overloads, it's essential to ensure that the provided signatures match the actual function implementation. Mismatched signatures can lead to unexpected behavior and type errors. Mismatched overload signatures:

```typescript
function greet(person: string): string;
function greet(person: string, age: number): string;
function greet(person: string, age?: number): string {
  if (age) {
    return `Hello, ${person}! You are ${age} years old.`;
  }
  return `Hello, ${person}!`;
}

const greeting = greet("Sergei", "Doe"); // #A
```

`#A Error: No overload matches this call`

In the example above, the second overload signature expects a number as the second argument, but the function call passes a string instead. This causes a type error, as no matching overload is found. This can be fixed by adding a matching overload signature:

```typescript
function greet(person: string): string;
function greet(person: string, age: number): string;
function greet(person: string, lastName: string): string;     // #A
function greet(person: string, ageOrLastName?: number | string): string {
  if (typeof ageOrLastName === "number") {
    return `Hello, ${person}! You are ${ageOrLastName} years old.`;
  } else if (typeof ageOrLastName === "string") {
    return `Hello, ${person} ${ageOrLastName}!`;
  }

  return `Hello, ${person}!`;
}

const greeting = greet("Sergei", "Doe");                      // #B
```

`#A Added correct overload signature`
`#B No error`

### 4.9.2. Having similar overloads

Similar overloads can result in ambiguous function signatures and make it difficult to understand which signature is being used in a specific context.

```typescript
function format(value: string, padding: number): string; // #A
function format(value: string, padding: string): string; // #A
function format(value: string, padding: string | number): string {
  if (typeof padding === "number") {
    return value.padStart(padding, " ");
  }

  return value.padStart(value.length + padding.length, padding);
}

const formatted = format("Hello", 5);                    // #B
```

`#A Overlapping overloads`
`#B It works but which overload is used?`

In the example above, the two signatures are very similar, as both accept a string as the first argument and have different types for the second argument. This can lead software engineers to confusion about which signature is being used in a given context. This is because it's not immediately clear which overload is being used when calling format("Hello", 5). While the TypeScript compiler can correctly infer the types and use the appropriate overload, the ambiguity may cause confusion for developers trying to understand the code.

A better approach would be to simply remove the overloads as shown in the following code listing:

```typescript
function format(value: string, padding: string | number): string {
  if (typeof padding === "number") {
    return value.padStart(padding, " ");
  }
  return value.padStart(value.length + padding.length, padding);
}

const formatted = format("Hello", 5); // Works!
```

Another approach if more parameters are needed is to enhance the overload signatures to avoid ambiguity, in this case padding with a string and specifying direction:

```typescript
function format(value: string, padding: number): string; // #A

function format(
  value: string,
  padding: string,
  direction: "left" | "right"
): string;                              // #B

function format(
  value: string,
  padding: string | number,
  direction?: "left" | "right"
): string {
  if (typeof padding === "number") {
    return value.padStart(padding, " ");
  } else {
    if (direction === "left") {
      return padding + value;
    } else {
      return value + padding;
    }
  }
}

const formatted = format("Hello", 5);   // #C
const formattedWithDirection = format("Hello", " ", "right");
```

`#A Padding with a number`
`#B Padding with a string and specifying direction`
`#C No ambiguity`

### 4.9.3. Applying excessive overloads

Using too many overloads can lead to increased code complexity and reduced readability. In many cases, using optional parameters, default values, or union types can simplify the function signature and implementation.

```typescript
function combine(a: string, b: string): string; // #A
function combine(a: number, b: number): number; // #A
function combine(a: string, b: number): string; // #A
function combine(a: number, b: string): string; // #A
function combine(a: string | number, b: string | number): string | number {
  if (typeof a === "string" && typeof b === "string") {
    return a + b;
  } else if (typeof a === "number" && typeof b === "number") {
    return a * b;
  } else {
    return a.toString() + b.toString();
  }
}

const result = combine("Hello", 5);             // #B
```

`#A Excessive overloads`
`#B Complex implementation with many overloads`

In the example above, using four overloads increases the complexity of the function. Simplifying the implementation by leveraging union types, optional parameters, or default values can improve readability and maintainability.

Excessive overloads can be fixed by getting rid of overloads and simplifying the function signature using union types:

```typescript
function combine(a: string | number, b: string | number): string | number {
  if (typeof a === "string" && typeof b === "string") {     // #A
    return a + b;
  } else if (typeof a === "number" && typeof b === "number") {
    return a * b;
  } else {
    return a.toString() + b.toString();
  }
}

const result = combine("Hello", 5);                         // #B
```

`#A Adding union types`
`#B Simplified implementation with union types`

In conclusion, using function overloads effectively can greatly enhance type safety and precision in your TypeScript code. However, it's important to avoid common mistakes, such as mismatched signatures, overlapping overloads, and excessive overloads, to ensure your code remains clean, maintainable, and bug-free.

## 4.10. Misapplying Function Types

TypeScript allows developers to define custom function types, which can be a powerful way to enforce consistency and correctness in your code. However, it's important to use these function types appropriately to avoid potential issues or confusion. In this section, we'll discuss some common misuses of function types and how to avoid them.

### 4.10.1. Overloading using function types

Function overloads provide a way to define multiple function signatures for a single implementation. However, overloading function types is not supported. Instead, use union types to represent the different possible input and output types or alternative solutions.

Here's an incorrect example of a function overload with type alias MyFunction (can also be an interface) with two function signatures. One takes numbers and returns a number. Second takes strings and returns a string. Yet, this code throws "Type '(x: string | number, y: string | number) => string | number' is not assignable to type 'MyFunction'.":

```typescript
type MyFunction = {
  (x: number, y: number): number;
  (x: string, y: string): string;
};

const myFunction: MyFunction = (x, y) => {  // #A
  if (typeof x === "number" && typeof y === "number") {
    return x + y;
  } else if (typeof x === "string" && typeof y === "string") {
    return x + " " + y;
  }
  throw new Error("Invalid arguments");
};

console.log(myFunction(1, 2));
console.log(myFunction("Hao", "Zhao"));
```

`#A Type '(x: string | number, y: string | number) => string | number' is not assignable to type 'MyFunction'.`

The correct example would have the type with unions where the declaration of a type alias MyFunction is defined as a function type that takes two parameters, x and y. Each parameter can be either a number or a string (as indicated by the | which denotes a union type). The function is expected to return either a number or a string:

```typescript
type MyFunction = (x: number | string, y: number | string) => number | string;
```

We can also use function declarations for overloads (instead of types):

```typescript
function myFunction(x: number, y: number): number;
function myFunction(x: string, y: string): string;
function myFunction(x: any, y: any): any {
  if (typeof x === "number" && typeof y === "number") {
    return x + y;
  } else if (typeof x === "string" && typeof y === "string") {
    return x.concat(" ").concat(y);
  }
  throw new Error("Invalid arguments");
}
```

In this version of the code, when x and y are strings, the function uses the concatenation method to combine them, which ensures that the operation is understood as string concatenation, not numerical addition.

An alternative example would have separate functions to avoid overloading functions (and type guards):

```typescript
type MyFunctionNum = {
  (x: number, y: number): number;
};

type MyFunctionStr = {
  (x: string, y: string): string;
};

const myFunctionStr: MyFunctionStr = (x, y) => {
  return x.concat(" ").concat(y);
};

const myFunctionNum: MyFunctionNum = (x, y) => {
  return x + y;
};

myFunctionNum(1, 2);
myFunctionStr("Hao", "Zhao");
```

### 4.10.2. Creating overly complicated function types

To illustrate the benefits of simplicity when it comes to function types, let's take a look at this example of a function type for a function that could be a basic calculator operation, where a and b are the numbers to be operated on, and op determines which operation to perform. If op is not provided, the function could default to one operation, such as addition:

```typescript
type CalculationOperation = (
  a: number,
  b: number,
  op?: "add" | "subtract" | "multiply" | "divide"
) => number;

const complexCalculation: CalculationOperation = (a, b, op = "add") => {
  switch (op) {
    case "add":
      return a + b;

    case "subtract":
      return a - b;

    case "multiply":
      return a * b;

    case "divide":
      return a / b;

    default:
      throw new Error(`Unsupported operation: ${op}`);
  }
};

console.log(complexCalculation(4, 2, "subtract"));  // #A
console.log(complexCalculation(4, 2));              // #B
```

`#A Outputs: 2`
`#B Outputs: 6 (default is 'add')`

This CalculationOperation function type specifies that a function assigned to it (complexCalculation) should accept two required parameters, a and b, both of which should be of type number. Additionally, it can accept an optional third parameter op, which is a string that can only be one of four specific operations corresponding to values: 'add', 'subtract', 'multiply', or 'divide'. This is achieved through the use of union types (denoted by the | character), which allow for a value of op to be one of several defined types. Finally, the function type definition also states that a function of this type should return a value of type number.

A better approach would use a simpler function type but four different functions. Each of these four functions is typed at CalculationOperation. By using the CalculationOperation type, the code ensures that all these functions follow the correct type signature. If any of these functions were implemented incorrectly (for example, if one of them tried to return a string), the TypeScript compiler would raise an error.

```typescript
type CalculationOperation = (a: number, b: number) => number;

const add: CalculationOperation = (a, b) => a + b;
const subtract: CalculationOperation = (a, b) => a - b;
const multiply: CalculationOperation = (a, b) => a * b;
const divide: CalculationOperation = (a, b) => a / b;
```

By using function types correctly, you can leverage TypeScript's type system to enforce consistency and improve the maintainability of your code.

### 9.10.3. Confusing function types with function signatures

A less common but still confusing mistake is to confuse the function types with the function signatures. In a nutshell, the function types describe the shape of a function, while the function signatures are the actual implementation of the function. Consider the following example in which we incorrectly confuse the function type definition with function definition:

```typescript
type MyFunction = (x: number, y: number) => number {        // #A
  return x + y;
};
```

`#A TypeScript error: ';' expected because we try to implement the function in a type alias.`

To fix this, we must separate the type from the function definition itself (as a function expression assigned to a variable of type MyFunction). Here's a correct code:

```typescript
type MyFunction = (x: number, y: number) => number;         // #A
const myFunction: MyFunction = (x, y) => x + y;             // #B
```

`#A Function type`
`#B Function implementation`

In light of this, TypeScript is giving us an error but the error is saying something about a semicolon and it's not immediately obvious. Reading about this blunder can save you a few minutes of confused staring at the code.

### 4.10.4. Using overly generic function types

Overly generic function types can lead to a loss of type safety, making it difficult to catch errors at compile time. For example, the following function type is too generic:

```typescript
type GenericFunction = (...args: any[]) => any;
```

This function type accepts any number of arguments of any type and returns a value of any type. Of course, as discussed previously, it lessens the benefits of TypeScript. It's much better to use more specific function types that accurately describe the expected inputs and outputs:

```typescript
type SpecificFunction = (a: number, b: number) => number;
```

We've covered a lot of ground in terms of applying function types and their best practices. To sum it up: types are good (instead of generic any or no types), simple is good (instead of overcomplicating).

## 4.11. Ignoring Utility Types for Functions

TypeScript provides a set of built-in utility types that can make working with functions and their types easier and more efficient. Ignoring these utility types can lead to unnecessary code repetition and missed opportunities to leverage TypeScript's type system to improve code quality. This section will discuss some common utility types for functions and provide examples of how to use them effectively.

### 4.11.1. Forgetting about typeof

In TypeScript, the typeof operator can be used to extract the type of a variable, including functions. When you use typeof with a function, it returns the type signature of the function, including its parameter types and return type. This can be particularly useful when you want to reuse the type signature of an existing function for another variable or for defining parameters or return types in other functions.

Here's an example to illustrate how you might use typeof to extract the type of a function:

```typescript
function exampleFunction(                               // #A
  a: number,
  b: string
): boolean {
  // ... some operations
  return true;
}

type ExampleFunctionType = typeof exampleFunction;      // #B
let myFunction: ExampleFunctionType;                    // #C

myFunction = (num: number, str: string): boolean => {   // #D
  // ... some operations
  return false;
};
```

`#A Defining a function (imagine it's outside your code and we can't change it)`
`#B Using 'typeof' to extract the function type`
`#C Now you can use the extracted type for other variables or parameters`
`#D Assigning a function to 'myFunction' that matches the 'exampleFunction' signature`

In this example: exampleFunction is a simple function that takes a number and a string as parameters and returns a boolean. Consider it being outside of your code so that it's impossible to change its code to use the same type as myFunction. Next, ExampleFunctionType uses typeof to extract the type signature of exampleFunction. This type includes the parameter types and return type of exampleFunction.

Then, myFunction is then declared with the type ExampleFunctionType, meaning it should be a function with the same signature as exampleFunction. By using typeof to extract and reuse function types, you maintain consistency and reduce redundancy, especially when dealing with complex functions or when you need to ensure multiple functions share the same signature across your codebase.

### 4.11.2. Underusing ReturnType for Better Type Inference

The ReturnType utility type extracts the return type of a function, which can be useful when you want to ensure that a function's return type is the same as another function's or when defining derived types.

Here's a less than ideal example that defines a function and a function type. The function named sum takes two arguments, a and b, both of type number. This function, when called with two numbers, adds those numbers together and returns the result, which is also of type number.

Then, the type alias named Calculation represents a function which takes two number arguments and returns a number. This type can be used to type-check other functions like multiply to ensure they match this pattern of taking two numbers and returning a number.

```typescript
function sum(a: number, b: number): number {              // #A
  return a + b;
}

type Calculation = (a: number, b: number) => number;      // #B

let multiply: Calculation = (a: number, b: number) => {   // #C
  return a * b;
};
```

`#A Implementing the addition function sum`
`#B Defining type alias`
`#C Implementing a multiplication function with the defined type alias`

In the example above, the return type of sum is manually defined inline as a number, and the same return type is specified again in type alias Calculation. Also, we can let TypeScript infer the type of multiply by having this (previously we covered how inference works and what are some of its pros and cons):

```typescript
let multiply: Calculation = (a, b) => {
  return a * b;
};
```

Interestingly, we would reuse the return type of the function sum. By using ReturnType, the return type of sum is automatically inferred and used in Calculation, reducing code repetition and improving maintainability.

```typescript
function sum(a: number, b: number) {
  return a + b;
}

type Calculation = (a: number, b: number) => ReturnType<typeof sum>;

let multiply: Calculation = (a: number, b: number) => {
  return a * b;
};
```

You may think that this example is silly because why wouldn't you use Calculation for sum directly as we did for multiply, instead of using ReturnType? That's because functions like sum can be defined in a different module or a library (authored by other developers) so we don't have rights to augment code for sum. At the same time, we want the return types to match. In situations like this ReturnType can come in handy.

Alternatively in _this particular_ example, you can replace the whole type like this:

```typescript
type Calculation = typeof sum;
```

However, that's a very different approach than just pulling the return type out of sum because it assigns the entire type not just return type. It's less flexible. This way we cannot modify parameters if we want but with ReturnType approach, the function parameters can be different for Calculation than for sum.

```typescript
type Calculation = (nums: number[]) => ReturnType<typeof sum>;

let multiply: Calculation = (nums) => {
  return nums.reduce((p, c) => p * c, 1);
};
```

Here's another more complex example of ReturnType that showcases the declaration of a function fetchData and a type FetchDataResult, followed by the definition of another function processData.

The function fetchData fetches some data from a given URL, a type FetchDataResult represents the result of the fetched data, and the function processData processes the fetched data using a provided fetch function callback.

The fetchData function return type is exactly the same as the return type of the callback function to processData:

```typescript
function fetchData(url: string): Promise<{ data: any }> {
  // Fetch data from the URL and return a Promise
}

type FetchDataResult = Promise<{ data: any }>;

function processData(fetchFn: (url: string) => FetchDataResult) {
  // Process the fetched data
}
```

The fetchData function takes a url parameter of type string and returns a Promise that resolves to an object with a data property of type any. This function is responsible for fetching data from the specified URL. The FetchDataResult type is defined as a Promise that resolves to an object with a data property of type any. This type is used to describe the expected return type of the fetchFn function parameter in the processData function. The processData function takes a function parameter fetchFn which is defined as a function accepting a url parameter of type string and returning a FetchDataResult. This function is responsible for processing the fetched data.

Hence, in the example above, the return type of fetchData is repeated twice, which can be error-prone and harder to maintain. And let's say we can update code for fetchData for some reason or another. Considering this, a better example would be leverage ReturnType to avoid code duplications that can lead to errors when modified only in one place and not all the places:

```typescript
function fetchData(url: string): Promise<{ data: any }> {   // #A
  // Fetch data from the URL and return a Promise
}

type FetchDataResult = ReturnType<typeof fetchData>;       // #B

function processData(fetchFn: (url: string) => FetchDataResult) {
  // Process the fetched data
}
```

`#A Imagine this is outside of our code in some library over which we don't have control`
`#B Used the ReturnType utility type`

Indeed, by using the ReturnType utility type, we simplify the code and make it easier to maintain.

### 4.11.3. Forgoing Parameters for Clearer Argument Types

The Parameters utility type extracts the types of a function's parameters as a tuple, making it easier to create types that have the same parameters as an existing function. It's somewhat similar to ReturnType, only for function parameters (a.k.a., function arguments).

Consider you have some default generic function that greets people standardGreet. Then if you want to create new custom functions, you can define a type alias MyGreeting that would be used to greet loudly or nicely:

```typescript
function standardGreet(name: string, age: number) {
  console.log(`Hello, ${name}. You are ${age} years old.`);
}

type MyGreeting = (name: string, age: number) => void;

const greetPersonLoudly: MyGreeting = (name, age) => {
  standardGreet(name.toUpperCase(), age);
};

const greetPersonNicely: MyGreeting = (name, age) => {
  standardGreet(name, age - 10);
};

greetPersonLoudly("Deepak", 54); // Hello, DEEPAK. You are 54 years old.
greetPersonNicely("Deepak", 54); // Hello, Deepak. You are 44 years old.
```

In the example above, the parameter types of standardGreet are manually specified again in MyGreeting. We can do better than that, right? Of course! Let's utilize Parameters to "extract" function parameters from standardGreet while the rest of the code can remain the same:

```typescript
type MyGreeting = (...args: Parameters<typeof standardGreet>) => void;
```

By using Parameters, the parameter types of standardGreet are automatically inferred and used in MyGreeting, making the code cleaner and more maintainable. Next, I would like to demonstrate a few more examples and use cases of Parameters. We start with this code:

```typescript
function standardGreet(name: string, age: number) {
  console.log(`Hello, ${name}. You are ${age} years old.`);
}

type MyGreeting = (...args: Parameters<typeof standardGreet>) => void;
```

The next example demonstrates using Parameters<typeof standardGreet> to assign specific arguments to params1 and then invoking standardGreet with the spread operator:

```typescript
const params1: Parameters<typeof standardGreet> = ['Pooja', 25];
greet(...params1);      // #A
```

`#A Output: Hello, Pooja. You are 25 years old.`

After that, the next example showcases the use of tuple types by declaring params2 with the as const assertion to ensure the literal types of the arguments:

```typescript
const params2: Parameters<typeof standardGreet> = ['Arjun', 30] as const;
greet(...params2);      // #A
```

`#A Output: Hello, Arjun. You are 30 years old.`

Subsequently, the next example declares a variable greetPerson of type MyReturnedGreeting which represents a function with the same parameters as standardGreet. What's powerful about Parameters or ReturnType (covered previously) is that we can mix and match them (i.e., combine them with different values than the "parent" type). The greetPerson is then invoked with specific arguments but a new return type (string), resulting in the expected output.

```typescript
type MyReturnedGreeting = (...args: Parameters<typeof standardGreet>) => string;

const greetPerson: MyReturnedGreeting = (name, age) =>
  console.log(`¡Saludos, ${name}! Tienes ${age} años.`);

greetPerson("Vikram", 35);   // #A
console.log("Jose", 42);     // #B
```

`#A Output: NO`
`#B Output: Saludos, Jose! Tienes 42 años.`

In conclusion, TypeScript's utility types for functions can help you create more efficient, maintainable, and expressive code. By leveraging utility types like ReturnType, and Parameters, you can reduce code repetition and make your codebase more resilient to changes. Always consider using utility types when working with functions in TypeScript to get the most out of the language's type system.

## 4.12. Summary

- Always specify return types for functions to ensure proper type checking and prevent unexpected behavior.

- Use optional and rest parameters judiciously, considering their impact on function behavior and readability. Always put optional parameters after the required parameters in the function signature calls. And put rest parameters last.

- Always specify the return type of a function to ensure type safety and provide clear expectations to callers.

- Leverage utility types like Parameters, ReturnType, and ThisParameterType to enhance type safety and improve code quality in functions.

- Use arrow functions or explicit binding to maintain the desired this context. Always set the shape/type of this. Understand the differences between bind, call, apply, and strictBindCallApply for manipulating the this context.

- Use globalThis instead of environment-specific global objects (window, global, etc.) for better portability.

- Utilize utility types like Parameters, ReturnType, and ThisParameterType to improve code quality and correctness.
