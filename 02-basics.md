

# 2.  Basic TypeScript Mistakes

**This chapter covers**

- Using any too often, ignoring compiler warnings
- Not using strict mode, incorrect usage of variables, and misusing optional chaining
- Overusing nullish
- Misusing of modules export and inappropriate use of type
- Mixing up == and ===
- Neglecting type inference

"You know that the beginning is the most important part of any work" said Plato. I add: "especially in the case of learning TypeScript". When many people learn basics (any basics not just TypeScript) the wrong way, it's much harder to unlearn them than to learn things from the beginning the proper way. For example, alpine skiing (which is also called downhill skiing, not to confuse with country skiing) is hard to learn properly. However, it's easy to just ski with bad basics. In fact, skiing is much easier than snowboarding because you can use two boards (skis) not one (snowboard). In skiing, things like angulation (the act of inclining your body and angling your knees and hips into the turn) don't come easy. I've seen people who ski for years incorrectly which leads to increased chances of trauma, fatigue and decreased control. We can extend the metaphor to TypeScript. Developers who omit the basics suffer more frustration (not a scientific fast, just my observation). By the way, why did the JavaScript file break up with the TypeScript file? Because it couldn't handle the "type" of commitment. Speaking of commitment, we need it to go through the book. Yes, basics are rarely serious f*ck ups as promised in the title but I promise we'll get to them!

## 2.1.  Using any Too Often

TypeScript's main advantage over JavaScript is its robust static typing system, which enables developers to catch type-related errors during the development process. However, one common mistake that developers make is using the any type too often. This section will discuss why relying on the any type is problematic and provide alternative solutions to handle dynamic typing more effectively. On that note, a JavaScript variable once said to the TypeScript variable: “I can be ANYthing I want to be!” ;-)

In TypeScript, the any type allows a variable to be of any JavaScript type, effectively bypassing the TypeScript type checker. This is basically what JavaScript does---allows a variable to be of any type and to change types at run time. It's even said that variables in JavaScript don't have types, but their values do. While this might certainly seem convenient in special situations, it can lead to issues such as:

- Weaker type safety: Using any reduces the benefits of TypeScript's type system, as it disables type checking for the variable. This can result in unnoticed runtime errors, defeating the purpose of using TypeScript.

- Reduced code maintainability: When any is used excessively, it becomes difficult for developers to understand the expected behavior of the code, as the type information is missing or unclear.

- Loss of autocompletion and refactoring support: TypeScript's intelligent autocompletion and refactoring support relies on accurate type information. Using any deprives developers of these helpful features, increasing the chance of introducing bugs during code changes.

Let's consider several TypeScript code examples illustrating the usage of any and its potential downsides: using any for a function parameter, for a variable and in an array:

```typescript
function logInput(input: any) {      // #A
  console.log(`Received input: ${input}`);
}

logInput("Hello");                  // #B
logInput(42);
logInput({ key: "value" });

let data: any = "This is a string"; // #C
data = 100;                         // #D

let mixedArray: any[] = [           // #E
  100, 
  true, 
  { key: "value" }
]; 
mixedArray.push("42"); 
mixedArray[mixedArray.length-1]+=1  // #F
```

`#A Using any for a function parameter`
`#B No type checking, any value is allowed`
`#C Using any for a variable`
`#D No type checking, we can assign any value to data`
`#E Using any in an array`
`#F Without type checking, 42+1 becomes 421 instead of 43`

How could this happen? The thought process may go like this: I have some code but with an error. What should I do?

```typescript
let data = "This is a string";
data = 100;       // #A
```

`#A Type '100' is not assignable to type 'string'.`

To fix, it's tempting to use any:

```typescript
let data: any = "This is a string";
data = 100;
```

In fact, is the error gone or not? The TypeScript error is gone, but the true error that is identifiable only by scrutinizing code or running (and finding out bugs) is STILL THERE! Hence, any is *rarely* a good solution to a problem.

In these examples, we use any for function parameters, variables, and arrays. While this allows us to work with any kind of data without type checking, it also introduces the risk of runtime errors, as TypeScript cannot provide any type safety or error detection in these cases.

To improve type safety, consider using specific types or generics instead of any.

When we use specific types for function parameters, it'll be immediately clear without even running the code what line is incompatible. Let's say we want to divide by 10, so the parameter must be a number. In this case, passing a "Hello" string is not a good idea as it won't be assignable to the type of number:

```typescript
function logInput(input: number) {
  console.log(`Received input: ${input}, divided by 10 is ${input/10}`);
}
logInput("Hello");    // #A
logInput(42);

```

`#A Error: Argument of type '"Hello"' is not assignable to parameter of type 'number'.`

Using specific types for variables can also save us from time wasted debugging. If we specify a union of string or number types, then anything else will raise a red flag by TypeScript:

```typescript
let data: string | number = "This is a string";
data = 100;         // #A
data = false;       // #B
```

`#A Okay: TypeScript checks that the assigned value is of the correct type`
`#B Error: Type 'boolean' is not assignable to type 'string | number'.`

We can create a special type for our array elements:
```typescript
type MixedArrayElement = boolean | number | object;
let mixedArray: MixedArrayElement[] = [
  100, 
  true, 
  { key: "value" }
];
mixedArray.push("42");      // #A
mixedArray.push(42)         // #B
```

`#A Argument of type '"42"' is not assignable to parameter of type 'MixedArrayElement'.`
`#B Okay`

As you saw, by avoiding any and using specific types or generics, you can benefit from TypeScript's type checking and error detection capabilities, making your code more robust and maintainabe.

Instead of resorting to the any type, developers can use the following alternatives:

- Type annotations: Whenever possible, specify the type explicitly for a variable, function parameter, or return value. This enables the TypeScript compiler to catch type-related issues early in the development process.

- Union types: In cases where a variable could have multiple types, use a union type (e.g., string | number) to specify all possible types. This provides better type safety and still allows for flexibility.

- Type aliases and interfaces: If you have a complex type that is used in multiple places, create a type alias (e.g., type TypeName) or an interface to make the code more readable and maintainable. Later, we'll see plenty of examples about how to create both of them.

- Type guards: Use type guards (e.g., typeof, instanceof, or custom type guard functions) to narrow down the type of a variable within a specific scope, improving type safety without losing flexibility. We'll cover type guards in more detail later.

- Unknown type: If you truly don't know the type of a variable, consider using the unknown type instead of any or omitting the type reference to let TypeScript infer the type. The unknown type enforces explicit type checking before using the variable, thus reducing the chance of runtime errors.

All in all, while the any type can be tempting to use for its flexibility, it should be avoided whenever possible to maximize the benefits of TypeScript's type system. By using type annotations, union types, type aliases, interfaces, type guards, type inference and the unknown type, developers can maintain type safety while still handling dynamic typing effectively.

## 2.2.  Ignoring Compiler Warnings

TypeScript is designed to help developers identify potential issues in their code early on by providing insightful error messages and warnings. In general, the key difference between them is that warnings are non-blocking compilation, advisory, and optional while errors are blocking compilation and severe. But what's interesting, TypeScript can "allow" certain errors. For example, this code will compile and run outputting 100, albeit with the error message about the type mismatch "Type '100' is not assignable to type 'string'":

```typescript
let data = "This is a string";
data = 100;
console.log(data)
```

In this sense, TypeScript is very different from other compiled languages like C++ where you can't run a program if it doesn't compile! The reason is that in TypeScript type checking and compilation/transpilation are independent processes. Hence, the paradox of seeing a type mismatch error in our editor, but *still* being able to generate the JavaScript code and run it (at a huge risk).

For simplicity's sake we'll treat errors and warnings as a single category, although it's possible to configure different TypeScript ESLint rules to be "error", "warn" or be "off". Here are some examples of TypeScript and ESLint TypeScript errors and warnings:

- Type mismatch: Assigning a value to a variable that is of a different type.

- Unknown identifiers: Using a variable that hasn't been defined prior.

- Missing properties: Omitting properties required by an interface.

- Unused variables: Declaring a variable or an import that is never used.

- Triple over double equals: Recommends using === and !== instead of == and != to avoid type coercion.

- const over let: Using let for variables that are never reassigned.

Ergo, TypeScript developers can ignore some TS errors, but ignoring these compiler and type check errors and warnings can lead to subtle bugs, decreased code quality, and runtime errors. Ignoring warnings kind of defeats the benefits of TypeScript. This section will discuss the importance of addressing compiler warnings and suggest strategies for effectively managing and resolving them.

It's good to review the consequences of ignoring compiler warnings, because ignoring compiler warnings can result in various problems, including:

- Runtime errors: Many compiler warnings indicate potential issues that could cause unexpected behavior or errors during runtime. Ignoring these warnings increases the likelihood of encountering hard-to-debug issues in production.

- Code maintainability: Unresolved compiler warnings can make it difficult for other developers to understand the code's intent or identify potential issues, leading to decreased maintainability.

- Type safety: TypeScript's type system is designed to catch potential issues related to types. Ignoring warnings related to type safety may result in type-related bugs.

- Increase noise: Having unsolved warnings in the code can quickly snowball into a massive technical debt that will pollute your build terminal with noise that is useless because no action is taken on them.

Here are TypeScript code examples illustrating the potential issues of ignoring compiler warnings, some of them come when the strict mode is on while others can be configured in tsconfig.json (TypeScript) or .eslintrc.js (TypeScript ESLint):

Example 1: Unused variables: Declaring a variable that is never used can lead to unnecessary code and confusion.

```typescript
function add(x: number, y: number): number {
  const result = x + y;
  const unusedVar = "This variable is never used.";           // #A
  return result;
}
```

`#A 'unusedVar' is declared but its value is never read.`

Example 2: Unused function parameters: Declaring a function parameter that is never used can indicate that the function implementation is incomplete or incorrect.

```typescript
function multiply(x: number, y: number, z: number): number {      // #A
  return x * y;
}
```

`#A 'z' is declared but its value is never read.`

Example 3: Implicit `any`: Using an implicit any type can lead to a lack of type safety and make the code less maintainable.

```typescript
function logData(data) {              // #A
  console.log(`Data: ${data}`);
}
```

`#A Parameter 'data' implicitly has an 'any' type.`

Example 4: Incompatible types: Assigning or passing values with incompatible types can lead to unexpected behavior and runtime errors.

```typescript
function concatStrings(a: string, b: string): string {
  return a + b;
}

const result = concatStrings("hello", 42);            // #A
```

`#A Argument of type 'number' is not assignable to parameter of type 'string'.`

Example 5: Missing return: Not providing a return statement for all cases when return type does not include undefined can lead to implicitly returning undefined which in turn can lead to a type mismatch.

```typescript
function noReturn(a: number): string {      // #A
  if (a) return a.toString()
  else {
    console.log('no input')
  }
}
```

`#A Function lacks ending return statement and return type does not include 'undefined'.`

In these examples, we saw different types of compiler warnings that might occur during TypeScript development.

By addressing these compiler warnings, you can improve the quality, maintainability, and reliability of your TypeScript code. Ignoring compiler warnings can result in unintended consequences and harder-to-debug issues in the future. Most importantly, having the warnings present and unsolved increases the decay and reduces the code quality (see the theory of broken windows). The noise of errors can hide an error that is truly important. Also, it increases the mental burden of having to remember what errors are "expected" and okay and what aren't.

To combat the warnings, let's take a look at some strategies for managing and resolving compiler warnings:

- Configure the compiler: Adjust the TypeScript compiler configuration (tsconfig.json) to match your project's needs. Enable strict mode and other strictness-related flags to ensure maximum type safety and catch potential issues early on. Make no-warnings part of the build, pre-commit, pre-merge and pre-deploy CI/CD checks. Initially it will block work because the warnings need to be dealt with, but eventually the quality will go up and the velocity too.

- Treat warnings as errors: Configure the TypeScript compiler and ESLint to treat *all* warnings (non-blocking) as errors (blocking), enforcing a policy that no code with warnings should be pushed to the repository. This approach ensures that all warnings are addressed before merging changes.

- Regularly review warnings: If you cannot fix all warnings right now, at least periodically review and address compiler warnings, even if they don't seem critical at the time. This practice will help maintain code quality and reduce technical debt. If you have a huge backlog of current warnings, have weekly or monthly TS warnings "parties" (BYOB) where you get engineers for 1-2 hours on a call to clean up the warnings.

- Refactor code: In some cases, resolving compiler warnings may require refactoring the code. Always strive to improve code quality and structure, ensuring that it adheres to the best practices and design patterns.

- Educate the team: Make sure that all team members understand the importance of addressing compiler warnings and are familiar with TypeScript best practices. Encourage knowledge sharing and peer reviews to ensure that the entire team is aware of potential issues and how to resolve them. Be relentless in code reviews by educating and guarding against code with warnings.

Compiler warnings in TypeScript are designed to help developers identify potential issues early in the development process. Ignoring these warnings can result in runtime errors, decreased maintainability, and reduced code quality. By configuring the compiler correctly, treating warnings as errors, regularly reviewing warnings, refactoring code, and educating the team, developers can effectively manage and resolve compiler warnings, leading to a more robust and reliable codebase... and hopefully fewer sleepless nights being woken up by a "pager" while being on call to try to keep the systems alive.

## 2.3.  Not Using Strict Mode

TypeScript offers a strict mode that enforces stricter type checking and other constraints to improve code quality and catch potential issues during development. This mode is enabled by setting the "strict" flag to true in the tsconfig (e.g., "strict": true in the tsconfig.json json file configuration file). Unfortunately, some developers overlook the benefits of using strict mode, leading to less robust codebases and increased chances of encountering runtime errors.

The benefits of using strict mode as follows:

- Enhanced type safety: Strict mode enforces stricter type checks, reducing the likelihood of type-related errors and making the codebase more reliable.

- Better code maintainability: With stricter type checking, the code becomes more predictable and easier to understand, which improves maintainability and reduces technical debt.

- Improved autocompletion and refactoring support: Strict mode can improve TypeScript's advanced autocompletion and refactoring features, making it easier for developers to write and modify code. This is because of the rules such as noImplicitAny that enforces to provide a type which in turn helps with autocomplete.

- Reduced potential for runtime errors: The stricter checks introduced by strict mode help catch potential issues early, reducing the chances of encountering runtime errors in production.

- Encouragement of best practices: By using strict mode, developers are encouraged to adopt best practices and write cleaner, more robust code.

Now, here are TypeScript code examples illustrating the differences between strict and non-strict modes:

Non-strict mode is okay with implicit any type for fn:

```typescript
function functionFactory(fn) {      // #A
  fn(100)
}

functionFactory(console.log);
functionFactory(42);                // #B
```

`#A No TS error, but Parameter 'fn' implicitly has an 'any' type, but a better type may be inferred from usage.`
`#B Runtime error calling a non-function.`

Strict mode (enable by setting "strict": true in the tsconfig.json file) and noImplicitAny are voicing concerns on an implicit any:
```typescript
function functionFactory(fn) {      // #A
  fn(100)
}

functionFactory(console.log);
functionFactory(42);
```

`#A Parameter 'fn' implicitly has an 'any' type.`

This error makes a developer to choose a type which in turn helps to track the runtime issue with calling a non-function:

```typescript
function functionFactory(fn: Function) {
  fn(100)
}

functionFactory(console.log);
functionFactory(42);        // #A
```

`#A Argument of type 'number' is not assignable to parameter of type 'Function'.`

Here's an example with a class Person:

```typescript
class Person {
  name: string;
  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}
```

With the non-strict mode, we don't see the problem until we run the code (and that's if we careful enough to spot the bug!):

```typescript
const person = new Person();
person.greet();     // #A
```

`#A No TS error, but runtime is not okay Hello, my name is undefined.`

On the other hand, with the strict mode (enable by setting "strict": true in the tsconfig.json file) and strictPropertyInitialization, we can spot that something is fishy:

```typescript
class PersonStrict {
  name: string;       // #A
  greet() {
    console.log(`Hello, my name is ${this.name}.`);
  }
}

const personStrict = new PersonStrict("Anastasia");
personStrict.greet();
```

`#A Property 'name' has no initializer and is not definitely assigned in the constructor.`

To fix it, we can initialize using the property name initializer or in the constructor:

```typescript
name: string = 'Anastasia';
constructor(name: string) {
  this.name = name;
}
```

In these succinct two examples, we demonstrate the differences between strict and non-strict modes in TypeScript in the focus of noImplicitAny and strictPropertyInitialization. The gist is that in non-strict mode, some type-related issues may be overlooked, such as implicit any types or uninitialized properties. Those are the big two! But there's more to strict. So, what exactly does 'strict' entail? It serves as a collective shorthand for seven distinct configurations. And these configurations include:

- noImplicitAny: Variables without explicit type annotations will have an implicit any type, which can lead to type-related issues. Strict mode disallows this behavior and requires explicit type annotations.

- noImplicit: This In strict mode, the this keyword must be explicitly typed in functions, reducing the risk of runtime errors caused by incorrect this usage.

- strictNullChecks: Strict null checks enforce stricter checks for nullable types, ensuring that variables of nullable types are not unintentionally used as if they were non-nullable.

- strictFunctionTypes: The strict function types flag enforces stricter checks on function types, helping catch potential issues related to incorrect function signatures or return types.

- strictPropertyInitialization: With strict property initialization, class properties must be initialized in the constructor or have a default value, reducing the risk of uninitialized properties causing runtime errors.

- strictBindCallApply: With strict bind call apply, when using fn.apply(), TypeScript would check the arguments types too as in a normal fn() call.

- alwaysStrict: Puts "use strict" on top of each compiled JS file.

To get the most out of TypeScript's features, it is highly recommended to enable strict mode in the tsconfig.json configuration file. And by the way, not using tsconfig.json or failing to commit it to the version control system (project repository) is another big mistake!

By enabling strict mode, developers can enhance type safety, improve code maintainability, and reduce the likelihood of runtime errors. This helps you catch potential issues early and improves the quality and maintainability of your TypeScript code. This practice ultimately leads to a more robust and reliable codebase, ensuring that the full potential of TypeScript's static typing system is utilized.

## 2.4.  Declaring Variables Incorrectly

TypeScript (and JavaScript too) provides various ways to declare variables, including let, const, and var. However, using the incorrect variable declaration can lead to unexpected behavior, bugs, and a less maintainable codebase. This section will discuss the differences between these variable declarations and best practices for using them. And along the lines of variables: why was the TypeScript variable feeling insecure? It didn't have a `type`! :-)

The differences between let, const, and var:

- let: Variables declared with let have block scope, meaning they are only accessible within the block in which they are declared. let variables can be reassigned after their initial declaration.

- const: Like let, const variables have block scope. However, they cannot be reassigned after their initial declaration, making them suitable for values that should not change throughout the program's execution.

- var: Variables declared with var have function scope, meaning they are accessible within the entire function in which they are declared. This can lead to unexpected behavior and harder-to-understand code due to variable hoisting, which occurs when variable declarations are moved to the top of their containing scope.

Here are TypeScript code examples illustrating incorrect variable declaration and how to fix them.

Example 1: Using var instead of let or const. Let's say we have a code with a var i:


```typescript
for (var i = 0; i < 10; i++) {
  console.log('counting ', i);
}

console.log(i);
```

When we output i which is 10, the i variable is accessible outside the loop scope, which can lead to unexpected behavior due to possible name collision (e.g., if there's another loop down the road of the i variable. The fix is to use let or const for variable declaration so that we get an error trying to access the variable outside of the scope:

```typescript
for (let j = 0; j < 10; j++) {
  console.log('counting ', j);
}

console.log(j);     //  #A
```

`#A Error: Cannot find name 'j'. The `j` variable is scoped to the loop and not accessible outside.`

Example 2: Incorrectly declaring a constant variable. Next, we have a constant that is defined with let (instead of const):

```typescript
let constantValue = 42;
// a lot of code here...
```

Later in the code, a developer mistakenly updates the variable `constantValue = 84;` and boom, we may have a bug.

The fix is to use const for constant variables:

```typescript
const constantValueFixed = 42;
constantValueFixed = 84;    // #A
```

`#A Error: Cannot assign to 'constantValueFixed' because it is a constant.`

Example 3: Incorrectly using let for a variable that is not reassigned. As an example, we have a userName that is not reassigned but we use let:

```typescript
let userName = "Anastasia";
console.log(`Hello, ${userName}!`);
```

The fix is to use const for variables that are not reassigned:

```typescript
const userNameFixed = "Anastasia";
console.log(`Hello, ${userNameFixed}!`);
```

It's worth noting that under the hood const allows TypeScript to pin down type by inferring a more precise type. Consider this example:

```typescript
const propLiteral = "type";   // type is "type" literal not a string
let propString = "type";      // type is string not "type" literal
```

As you can see, with propLiteral TypeScript inferred the type to be a string literal, not just generally the string type.

It's vital to note that the immutability offered by const declarations ensures that variable binding (variable names) is immutable but not necessarily that the value itself is immutable. It can be very surface level when it comes to values, especially when values are objects. For objects, object-like values and arrays, while you can't reassign them directly, their properties can still be modified. In other words, const in JavaScript/TypeScript ensures that the variable is immutable but not necessarily the value it references (as is the case with objects). Let's take a look at a few examples.

For primitive values (like numbers, strings, booleans, `undefined`, and `null`), this distinction is a bit nuanced, since the value and the variable binding are effectively the same thing. Once a primitive is assigned, it cannot change. For example, when you do: const x = 10; You cannot reassign x to a new value.

However, for non-primitive values (like objects and arrays), the const keyword only means you can't change the reference the variable points to, but the internals of the object or array can be modified. Here's an example:

```typescript
const obj = { key: 'value' };
obj.key = 'newValue';       // #A
const arr = [1, 2, 3];
arr.push(4);                // #A
obj = { key: 'anotherValue' };      // #B
arr = [4, 5, 6];                    // #B
```

`#A This is allowed`
`#B TypeError: Assignment to constant variable.`

In the example above, while we can't reassign obj and arr to new objects or arrays, we can still modify their internal values.
If you want to make the object's properties (or the array's values) themselves immutable, you'd need additional measures like Object.freeze(). However, even Object.freeze() provides shallow immutability. If an object contains nested objects, you'd need a deep freeze function to make everything immutable. Here's an example of a recursive deepFreeze() function that leverages shallow Object.freeze():

```typescript
function deepFreeze(obj: any): any {
  
  const propNames = Object.getOwnPropertyNames(obj);            //  #A  
  propNames.forEach(name => {                                   //  #B
    const prop = obj[name];    
    if (typeof prop == 'object'                                 //  #C
      && prop !== null 
      && !Object.isFrozen(prop)) {
      deepFreeze(prop);
    }
  });

  return Object.freeze(obj);                                    // #D
}
```

`#A Retrieve the property names defined in obj`
`#B Freeze properties before freezing the object itself`
`#C If prop is an object or array and not already frozen, recurse`
`#D Freeze the object`

As a rule of thumb, here are the best practices for variable declarations:

- Prefer const by default: When declaring variables, use const by default, as it enforces immutability and reduces the likelihood of unintentional value changes. This can lead to cleaner, more predictable code.

- Use let when necessary: If a variable needs to be reassigned, use let. This ensures the variable has block scope and avoids potential issues related to function scope.

- Avoid var: In most cases, avoid using var, as it can lead to unexpected behavior due to function scope and variable hoisting. Instead, use let or const to benefit from block scope and clearer code.

- Use descriptive variable names: Choose clear, descriptive variable names that convey the purpose and value of the variable. This helps improve code readability and maintainability.

- Initialize variables: Whenever possible, initialize variables with a (default) value when declaring them. This helps prevent issues related to uninitialized variables and ensures that the variable's purpose is clear from its declaration.

By following these best practices for variable declaration, developers can create more maintainable, predictable, and reliable TypeScript codebases. By preferring const, using let when necessary, avoiding var, and choosing descriptive variable names, developers can minimize potential issues related to variable declaration and improve the overall quality of their code.

## 2.5. Misusing Optional Chaining

Optional chaining is a powerful feature introduced in TypeScript 3.7 and then later to JavaScript in ECMAScript 2020 (a.k.a., ES11). Optional chaining allows developers to access deeply nested properties within an object without having to check for the existence of each property along the way. While this feature can lead to cleaner and more concise code, it can also be misused, causing unexpected behavior and potential issues. This section will discuss the proper use of optional chaining and common pitfalls to avoid.

Optional chaining uses the question mark ? operator to access properties of an object or the result of a function call, returning undefined if any part of the chain is null or undefined. This can greatly simplify code that involves accessing deeply nested properties.

In this example we use three approaches. The first is without optional chaining and can break the code. The second is better because it uses && which will help to not break the code. The last example uses optional chaining and more compact than example with &&:

```typescript
interface User {
  name: string;
  address?: {                                     // #A
    street: string;
    city: string;
    country: string;
  },
}

const userWithAddress: User = {
  name: "Sergei",
  address: {                                    // #A
    street: "Main St",
    city: "New York",
    country: "USA",
  },
};

const user: User = {
  name: "Sergei",
};

const cityDirectly = userWithAddress.address.city;
const cityDirectlyUndefined = user.address.city;                        // #B

const city = userWithAddress.address && userWithAddress.address.city;   // #C
const cityUndefined = user.address && user.address.city;

const cityChaining = userWithAddress?.address?.city;                    // #D
const cityChainingUndefined = user?.address?.city;
```

`#A Optional field`
`#B Run-time error: undefined is not an object (evaluating 'user.address.city')`
`#C Without optional chaining - working code`
`#D With optional chaining`

In the preceding snippet, the values for city and cityChaining are "New York" and the values for cityUndefined and CityChainingUndefined are "undefined". The code breaks at run-time on cityDirectlyUndefined. Depending on the TypeScript configurations, developers can get a very convenient warning "'userWithAddress.address' is possibly 'undefined' and 'user.address' is possibly 'undefined'" for both cityDirectly and cityDirectlyUndefined. Thus, the optional chaining provides the safest and most eloquent way to access properties on the objects. Let's cover the proper use of Optional Chaining which includes the following:

- Use optional chaining to access deeply nested properties: When accessing properties several levels deep, use optional chaining to simplify the code and make it more readable.

- Combine optional chaining with nullish coalescing: Use the nullish coalescing operator ?? in conjunction with optional chaining to provide a default value when a property is null or undefined.

While we'll cover more on nullish coalescing in the next section, here's a short example:

```typescript
const city = user?.address?.city ?? "Unknown";
```

Here is the list of common pitfalls to avoid when working with TypeScript's Optional Chaining:

- Overusing optional chaining: While optional chaining can simplify code, overusing it can make the code harder to read, understand and debug. For example, when too many nested properties are optional instead of some or most of them being required, that can impair the readability. Use optional chaining judiciously and only when it provides clear benefits.

- Ignoring potential issues: Optional chaining can mask potential issues in the code, such as incorrect property names or unexpected null or undefined values. Going back to the preceding example with user, if API changes the property name address to userAddress, then the code will still work but always return undefined, making it hard to identify the source of the problem like inconsistency or typo. Ensure that your code can handle these cases gracefully and consider whether additional error handling or checks are necessary.

- Misusing with non-optional properties: Be cautious when using optional chaining with properties that should always be present. This can lead to unexpected behavior at runtime (e.g., missing required property causing a crash), and may indicate a deeper issue in the code that needs to be addressed.

By using optional chaining properly and avoiding common pitfalls, developers can write cleaner and more concise code while accessing deeply nested properties. Combining optional chaining with nullish coalescing (??) can further improve code readability and ensure that default values are provided when necessary. However, it is crucial to use optional chaining judiciously and remain aware of potential issues that it may mask.

## 2.6. Not Using Nullish Coalescing

Nullish coalescing is a helpful feature that allows developers to provide a default value when a given expression evaluates to null or undefined. The nullish coalescing operator ?? simplifies handling default values in certain situations. However, overusing nullish coalescing can lead to less readable code and potential issues. This section will discuss when to use nullish coalescing and when to consider alternative approaches.

Let's begin with understanding the nullish coalescing. The nullish coalescing operator ?? returns the right-hand operand when the left-hand operand is null or undefined. If the left-hand operand is any other value, including false, 0, or an empty string, it will be returned.

Example:

```typescript
const name = userInput?.name ?? "Anonymous";
```

Appropriate use of nullish coalescing:

- Providing default values: Use nullish coalescing to provide a default value when a property or variable might be null or undefined.

- Simplifying conditional expressions: Nullish coalescing can simplify conditional expressions that check for null or undefined values, making the code more concise.

- Combining with optional chaining: Use nullish coalescing in conjunction with optional chaining to access deeply nested properties and provide a default value when necessary.

Pitfalls and alternatives when working with nullish coalescing:

- Misinterpreting falsy values: Nullish coalescing only checks for null and undefined. Be cautious when using it with values that are considered falsy but not nullish, such as false, 0, or an empty string. In these cases, review the logic that needs to be implemented. If falsy values are needed to be considered as uninitialized values (rarely the case), then consider using the logical OR operator (||) instead. We covered the differences between logical OR and nullish coalescing.

It's worth focusing more on the difference between good old logical OR and nullish coalescing when it comes to initializing the default values for variables. In TypeScript (as well as in JavaScript starting with ES2020), both nullish coalescing (??) and the logical OR (||) can be used to provide default values. However, they behave differently in specific scenarios. Here's a breakdown of their differences:

With the logical OR (||), the way it works is it returns the right-hand side operand if the left-hand side operand is falsy. As you know, falsy values in JavaScript are `false`, `null`, `undefined`, `0`, `NaN`, `""` (empty string), and `-0`. Therefore, if the left-hand side is any of these values, the right-hand side (default/initial) value will be returned.

Here's an example of logical OR with an empty string, zero, null and undefined:

```typescript
const result1 = "" || "default";    // result1 = "default"
const result2 = 0 || 42;            // result2 = 42
const result3 = null || 42;         // result3 = 42
const result4 = undefined || 42;    // result4 = 42
```

On the other hand, the nullish coalescing (??) behaves slightly differently. It returns the right-hand side operand only if the left-hand side operand is null or undefined. It does not consider other falsy values (like `0`, `""`, or `NaN`) as trigger conditions. This means it's more specific in its operation compared to ||.

Here's an example of nullish coalescing with an empty string, zero, null and undefined:

```typescript
const result1 = "" ?? "default";    // result1 = "" (because the empty string is not null or undefined)
const result2 = 0 ?? 42;            // result2 = 0 (because 0 is not null or undefined)
const result3 = null ?? 42;         // result3 = 42
const result4 = undefined ?? 42;    // result4 = 42
```

In conclusion: use || when you want to provide a default value for any falsy value; and use ?? when you specifically want to provide a default only for null or undefined (recommended).

In TypeScript, the distinction becomes even more important because of the typing system, which allows for more specific handling of values and their types. The nullish coalescing operator (??) can be particularly useful when dealing with optional properties or values that might be set to null or undefined.

To illustrate a potential pitfall with logical OR, consider another example in which 0 could be a correct input, e.g., 0 volume, 0 index in an array, 0 discount. However, because of the truthy check of logical OR (||) our equation will fall back to the default value (which we don't want to happen). The following code is *incorrect,* because defaultValue will be used if inputValue is 0:

```typescript
const value = inputValue || defaultValue;
```

In this case (where 0 could be a valid value), the correct way is to use ?? or check for null. The following two alternatives are correct because defaultValue will be used only if inputValue is null or undefined, but not when it's 0:

```typescript
const value = inputValue ?? defaultValue;
const value = inputValue != null ? inputValue : defaultValue;
```

While nullish coalescing is a wonderful addition to a TypeScript programmer's toolbox, it's worth noting two items to watch out for:

- Overusing nullish coalescing: Relying too heavily on nullish coalescing can lead to less readable code and may indicate a deeper issue, such as improperly initialized variables or unclear code logic. Evaluate whether nullish coalescing is the best solution or if a more explicit approach would be clearer.

- Ignoring proper error handling: Nullish coalescing can sometimes be used to mask potential issues or errors in the code. Ensure that your code can handle cases where a value is null or undefined gracefully and consider whether additional error handling or checks are necessary.

By using nullish coalescing appropriately and being aware of its pitfalls, developers can write cleaner and more concise code when handling default values. However, it is crucial to understand the nuances of nullish coalescing and consider alternative approaches when necessary to maintain code readability and robustness.

## 2.7. Not Exporting/Importing Properly

Modularization is an essential aspect of writing maintainable and scalable TypeScript code. By separating code into modules, developers can better organize and manage their codebase. However, a common mistake is misusing of modules export or import, which can lead to various issues and errors. This section will discuss the importance of properly exporting and importing modules and provide best practices to avoid mistakes.

Let's start with defining what a module is and what exporting means. A module is a file that contains TypeScript code, including variables, functions, classes, or interfaces. Modules allow developers to separate code into smaller, more manageable pieces and promote code reusability. Exporting a module (or rather symbols in a module) means making its contents available to be imported and used in other modules. Importing a module allows developers to use the exported contents of that module in their code. A module in TypeScript can have several exported symbols (e.g., objects, classes, functions, types) and/or one default exported symbol.

There is the modern syntax to export and import modules in TypeScript (and JavaScript) that uses export and import statements. It's called ES6/ECMAScript 2015 module imports. It is supported by all modern browsers as of this writing and all main bundlers (tools that create web "binary").

The old and *not* recommended approaches include but not limited to:

- CommonJS/CJS: The syntax uses require and module.exports; and it's a legacy of Node.js.

- Loaders like Asynchronous Module Definition, Universal Module Definition, SystemJS and so on

- Immediately Invoked Function Expression (IIFE) and an HTML script tag: while IIFE is created to wrap the exported module to avoid global scope pollution of variable names, the script tag loading (either static in HTML or dynamic with JS injecting the DOM element) is still widely used for analytics and marketing services.

There are many reasons why these methods are no longer recommended with the main one being that ES6 modules are a widely adopted standard and supported by many libraries and browsers. ESMs have static analysis (which means imports and exports are determined at compile time rather than at a runtime) which gives us tree shaking, predictability, autocomplete and faster lookups. Also, when consistently using ES6 modules across all your code base, we can avoid errors, because the mechanisms and syntax of ES6 modules differ from CJS. Even Node.js now has support for ESM!

The best practices for exporting and importing modules:

- Use named exports: Prefer named exports over default exports, which allow for exporting multiple variables, functions, or classes from a single module. Also, named exports make it clear which items are being exported and allow for better code organization.

```typescript
// user-module.ts: Exporting named symbols
export const user = { /*...*/ };
export function createUser() { /*...*/ };

// Importing named symbols
import { user, createUser } from './user-module';
```

- Avoid using the default exports even for single exports: If a module only exports a single item, such as a class or function, there's a temptation to use a default export. This can simplify imports and make the code more readable.

```typescript
// user.ts: Exporting a default symbol
export default class User { /*...*/ };

// Importing a default symbol
import User from './user';
```

However, opting for a default export requires the importer to decide on a name for the symbol. This can lead to varied names for the same symbol across your codebase. It's advisable to use a named export to promote uniform naming conventions. Here's an example of a bad (confusing) naming of the imported defaults:

```typescript
// developer.ts: Exporting a default symbol
export default class Developer { /*...*/ };

// tester.ts: Exporting a default symbol
export default class Tester { /*...*/ };

// Importing default symbols
import User1 from './developer';
import User2 from './tester';
```

- Organize imports and exports: Keep imports and exports organized at the top of your module files. This helps developers understand the dependencies of a module at a glance and makes it easier to update or modify them.

- Be mindful of circular dependencies: Circular dependencies occur when two or more modules depend on each other, either directly or indirectly. This can lead to unexpected behavior and runtime errors. To avoid circular dependencies, refactor your code to create a clear hierarchy of dependencies and minimize direct coupling between modules. For what it's worth, circular dependency is less of a problem with static ESM because there's no evaluating order. This is another good reason to use them over CJMs, script tag injection or dynamic ESM (import()) which leads us to the next point.

- Avoid using dynamic imports when possible: Dynamic imports are mainly CJMs, script tag injections and import(). Indeed, dynamic imports can provide a certain flexibility by allowing to load modules at a runtime, so that the exact name doesn't have to be known before running the program. However, they can cause more trouble than the benefits they bring.

- Export types properly: Use the type keyword for exporting only types and the consistent-type-imports ESLint plugin to warn about not using it. The type keyword will tell TypeScript that this module exists only in the type system and not a runtime and it can be dropped during the transpilation.

- Avoid importing unused variables: Importing variables that are not used in the code can lead to code bloat, decreased performance, and reduced maintainability. Many IDEs and linters can warn you about unused imports, making it easier to identify and remove them.

- Leverage bundles for tree shaking: Using tools like Webpack or Rollup can help with tree shaking, which is the process of removing unused code during the bundling process. By being mindful of unused imports and addressing them promptly, developers can keep their code clean and efficient and their web binaries (bundles) small which leads to improved loading time for end users.

Some common mistakes to avoid when importing and exporting modules in plain JavaScript are abundant:

- Forgetting to export: Ensure that you export all necessary variables, functions, classes, or interfaces from a module to make them available for import in other modules.

- Incorrectly importing: Be cautious when importing modules and double-check that you are using the correct import syntax for named or default exports. Misusing import syntax can lead to errors or undefined values.

- Missing import statements: Ensure that you import all required modules in your code. Forgetting to import a module can result in runtime errors or undefined values.

Luckily for us they are not a big deal in TypeScript, because it has our back. Omitted or incorrectly formatted imports will result in type checking errors. A significant benefit of TypeScript is its ability to provide a safety net, catching these kinds of mistakes, which allows us to code with more confidence compared to plain JS.

By properly exporting and importing modules, developers can create maintainable and scalable TypeScript codebases. Following best practices and being cautious of common mistakes helps avoid issues related to module management, ensuring a more robust and organized codebase.

## 2.8. Not Using or Misusing Type Assertions

Type assertions are a feature in TypeScript that allows developers to override the inferred type of a value, essentially telling the compiler to trust their judgment about the value's type. Type assertion uses the as keyword. While type assertions can be useful in certain situations, their inappropriate use can lead to runtime errors, decreased type safety, and a less maintainable codebase. This section will discuss when to use type assertions and how to avoid their misuse.

Type assertions are a way of informing the TypeScript compiler that a developer has more information about the type of a value than the type inference system. Type assertions do not perform any runtime checks or conversions; they are purely a compile-time construct.

Here's an example where we define a variable with unknown type but must assert before going further (or use type guards) to avoid the error "Type 'unknown' is not assignable to type 'string'.:"

```typescript
const unknownValue: unknown = "Hello, TypeScript!";
const stringValue: string = unknownValue as string;
```

The appropriate use of type assertions include:

- Working with unknown types: Type assertions can be helpful when working with the unknown type, which may require an explicit type assertion or type guards before it can be used.

- Narrowing types: Type assertions can be used to narrow down union types or other complex types to a more specific type, provided the developer has a valid reason to believe the type is accurate. The most common use case is removing null and undefined from union types (T|null), i.e., null assertion operator val!.

- Interacting with external libraries: When working with external libraries that have insufficient or incorrect type definitions, type assertions may be necessary to correct the type information.

Here's an example of a good use of type assertion in which we know for sure that the element is an image:

```typescript
const el = document.getElementById("foo")
console.log(el.src)     // #A
const el = document.getElementById("foo") as HTMLImageElement
console.log(el.src)     // #B
```

`#A Property 'src' does not exist on type 'HTMLElement'.`
`#B All is good here now`

A little side note on type casting and type assertion. In TypeScript, they can be synonyms. However, TypeScript type assertion is different from the type casting in other languages like Java, C or C++ in that in other languages casting changes the value at a runtime to a different type. This is contrary to TypeScript's "casting" thatdoesn't change the runtime behavior of the program but provides a way to override the inferred type in the TypeScript type-checking phase. This is because TypeScript's "casting" will be stripped at run time when we run plain JavaScript.

Here are some pitfalls and alternatives to type assertions in TypeScript:

- Overusing type assertions: Relying too heavily on type assertions can lead to less type-safe code and may indicate a deeper issue with the code's design. Evaluate whether a type assertion is the best solution or if a more explicit approach would be clearer and safer.

- Ignoring type errors: Type assertions can be misused to bypass TypeScript's type checking system, which can lead to runtime errors and decreased type safety. Always ensure that a type assertion is valid and necessary before using it.

- Using type assertions instead of type declarations: If it's feasible, type declarations are preferred because they perform excessive property checking while type assertions perform a much more limited form of type checking.

Here's an example of using type declaration and type assertions for variable initialization. They both work but type declaration is preferred:

```typescript
const declaredInstanceOfSomeCrazyType: SomeCrazyType = {...};
const assertedInstanceOfSomeCrazyType = {...} as SomeCrazyType;
```

- Bypassing proper type guards: Instead of using type assertions, consider implementing type guards to perform runtime checks and provide better type safety. Type guards are functions that return a boolean value, indicating whether a value is of a specific type. We'll cover them in more detail later.

Here's an example in which we illustrate two approaches: type guards and type assertions. Let's say we have some code that takes strings and numbers. We get an error Object is of type 'unknown' when we use value as number because TypeScript is unsure:

```typescript
function plusOne(value: unknown) {
  console.log(value+'+1')
  console.log((value)+1)      // #A
}

plusOne('abc')
plusOne(123)
```

`#A Object is of type 'unknown' because TypeScript is unsure about the value type inferred from usage`

To fix it, we can use type assertion like this:

```typescript
console.log((value as number)+1)
```

Or we can use type guards to instruct TypeScript about types of value as follows:

```typescript
function plusOne(value: unknown) {
  if (typeof value === 'string') {
    console.log(value+'+1')
  } else if (typeof value ==='number') {
    console.log(value+1)
  }
}

plusOne('abc')
plusOne(123)
```

Chaining type assertions with unknown: In some cases, developers may find themselves using a pattern like unknownValue as unknown as knownType to bypass intermediary types when asserting a value's type. While this technique can be useful in specific situations, such as working with poorly typed external libraries or complex type transformations, it can also introduce risks. Chaining type assertions in this way can undermine TypeScript's type safety and potentially mask errors. Use this pattern cautiously and only when necessary, ensuring that the assertion is valid and justified. Whenever possible, consider leveraging proper type guards, refining type definitions, or contributing better types to external libraries to avoid this pattern and maintain type safety.

By using type assertions appropriately and being aware of their pitfalls, developers can write cleaner, safer, and more maintainable TypeScript code. Ensure that type assertions are only used when necessary and consider alternative approaches, such as type guards, to provide better type safety and runtime checks.

## 2.9. Checking for Equality Improperly

When comparing values in TypeScript, it is crucial to understand the difference between the equality operator '==' and the strict equality operator '==='. Mixing up these two operators can lead to unexpected behavior and hard-to-find bugs. Also, we should keep in mind the necessity of deep comparison for objects (and object-like types, i.e., arrays). This section will discuss the differences between the two operators, their appropriate use cases, and how to avoid common pitfalls and then wrap up with examples of deep comparison.

The equality operator '==' compares two values for equality, returning true if they are equal and false otherwise. However, '==' performs type coercion when comparing values of different types, which can lead to unexpected results. For example, this line prints/logs true because the number 42 is coerced to the string "42":

```typescript
console.log(42 == "42");
```

On the other hand, the strict equality operator '===' compares two values for equality, considering both their value and type. No type coercion is performed, making '===' a safer and more predictable choice for comparison. For example, the following statement prints/logs false because 42 is a number and "42" is a string:

```typescript
console.log(42 === "42");
```

Please note that TypeScript will warn us with the message: This comparison appears to be unintentional because the types 'number' and 'string' have no overlap. Good job, TypeScript!

The best practices for using '==' and '===' are as follows:

- Prefer '===' for comparison: In most cases, use '===' when comparing values, as it provides a more predictable and safer comparison without type coercion.

- Use '==' with caution (if at all): While there might be situations where using '==' is convenient (e.g., x == null serves as a handy method to verify if `x` is either `null` or `undefined`), be cautious and ensure that you understand the implications of type coercion. If you need to compare values of different types, consider converting them explicitly to a common type before using '=='.

- Leverage linters and type checkers: Tools like ESLint can help enforce the consistent use of '===' and warn you when '==' is used, reducing the risk of introducing bugs.

The common pitfalls to avoid when comparing values:

- Relying on type coercion: Avoid relying on type coercion when using '=='. Type coercion can lead to unexpected results and hard-to-find bugs. Instead, use '===' or explicitly convert values to a common type before comparison.

- Ignoring strict inequality '!==': Similar to the strict equality operator '===', use the strict inequality operator '!==' when comparing values for inequality. This ensures that both value and type are considered in the comparison.

- Confusing reference and value equality checks. When comparing objects or arrays (special objects) are passed by reference so using === won't cut it, because in this case the references would be compared not values. In other words, when comparing two objects, true will be returned only if it's the same object (and false in all other cases, even when their properties are not equal). Thus, it's important to remember that for objects we need to perform a deep comparison, that is a comparison that is performed for each child value no matter how deep the nested structure is. Methods such as lodash.deepEqual or at the very least JSON.stringify() can come in handy.

By understanding the differences between '==' and '===' and following best practices, developers can write more predictable and reliable TypeScript code. Using strict equality and strict inequality operators ensures that type coercion does not introduce unexpected behavior, leading to a more maintainable and robust codebase.

In certain cases, you may want to perform a deep comparison of objects or complex types, for which neither '==' nor '===' is suitable. In such situations, you can use utility methods provided by popular libraries, such as Lodash's isEqual function. The isEqual function performs a deep comparison between two values to determine if they are equivalent, taking into account the structure and content of objects and arrays. This can be particularly helpful when comparing objects with nested properties or arrays with non-primitive values. Keep in mind, though, that using utility methods like isEqual may come with a performance cost, especially for large or deeply nested data structures.

Here's a simple implementation of a deep equal comparison method for objects with nested levels in TypeScript:

```typescript
function deepEqual(obj1: any, obj2: any): boolean {
  if (obj1 === obj2) {
    return true;
  }

  if (typeof obj1 !== 'object' 
    || obj1 === null 
    || typeof obj2 !== 'object' 
    || obj2 === null) {
    return false;
  }

  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);

  if (keys1.length !== keys2.length) {
    return false;
  }

  for (const key of keys1) {
    if (!keys2.includes(key)) {
      return false;
    }

    if (!deepEqual(obj1[key], obj2[key])) {
      return false;
    }
  }

  return true;
}
```

This deepEqual function compares two objects recursively, checking if they have the same keys and the same values for each key. It works for objects with nested levels and arrays, as well as primitive values. However, this implementation does not handle certain edge cases, such as handling circular references or comparing functions.

Keep in mind that deep comparisons can be computationally expensive, especially for large or deeply nested data structures. Use this method with caution and consider using optimized libraries, such as Lodash, when working with complex data structures in production code.

## 2.10. Not Understanding Type Inference

TypeScript is known for its strong type system, which helps developers catch potential errors at compile-time and improve code maintainability. One powerful feature of TypeScript's type system is type inference, which allows the compiler to automatically deduce the type of a value based on its usage. Not understanding type inference can lead to unexpected errors. This section will discuss type inference and provide best practices for utilizing it effectively.

Let's begin with understanding TypeScript's type inference. Type inference is the process by which TypeScript automatically determines the type of a value without requiring an explicit type annotation from the developer. This occurs in various contexts, such as variable assignments, function return values, and generic type parameters.

Here's a short example of type inference:

```typescript
const x = 42;      // #A
let y;             // #B
y = 1;
let z = 10;        // #C
z = 2;

function double(value: number) {    // #D
  return value * 2;
}
```

`#A TypeScript infers the type of x to be number literal because x is immutable`
`#B TypeScript infers the type of any`
`#C TypeScript infers the type of number (widens), not literal because z is mutable`
`#D TypeScript infers the return type of the function to be number`

The best practices for utilizing type inference:

- Provide type annotation when necessary: In some cases, TypeScript's type inference may not be able to deduce the correct type, or you might want to enforce a specific type. In these situations, provide an explicit type annotation to guide the compiler.

- Provide type annotations as much as possible: Explicit annotations can make the intent clear, especially in more complex scenarios. (This point can come as my personal opinion, and there are still debates around the decision to explicitly annotate the return type of a function or rely on TypeScript's inference is often debated. Embracing type inference allows TypeScript to infer the type of a value which in turn reduces code verbosity.)

- Use contextual typing: TypeScript's contextual typing allows the compiler to infer types based on the context in which a value is used. For example, when assigning a function to a variable with a specific type Callback, TypeScript can infer the types of the function's parameter data and the return value.

```typescript
type Callback = (data: string) => void;
const myCallback: Callback = (data) => {
  console.log(data);
};

myCallback('123')       // #A
```

`#A No need for type annotations here!`

A typical scenario for this is when you pass a callback to methods like map or filter. In such cases, TypeScript can deduce the type of the function's parameter from the array's type:

```typescript
const numbers = [1, 2, 3];
const squared = numbers.map(num => num * num);      // #A
```

`#A No need for type annotations here!`

- Leverage type inference for generics: TypeScript can infer generic type parameters based on the types of arguments passed to a generic function or class. Take advantage of this feature to write more concise and flexible code.

```typescript
function identity<T>(value: T): T {
  return value;
}

const result = identity([1, 2, 3]);     // #A
```

`#A TypeScript infers the type parameter T to be an array of numbers`

The common pitfalls to avoid when utilizing type inference in TypeScript come down to:

- Don't be afraid of an over-annotation: While over-annotating can make the code more verbose, less convenient to write and harder to maintain, don't be afraid of providing type annotations for values even when TypeScript can already infer the correct type.

- Ignoring type inference capabilities: Be aware of TypeScript's type inference capabilities and potential errors that it can introduce. For example, if function a is defined as function a() { return b(); }, any change in the return type of b will automatically reflect in the inferred return type of a. However, if you had provided an explicit type annotation for a, this automatic update wouldn't occur.

Embracing type inference or over-annotating, it's your choice but you need to understand type inference no matter what. Type inference lets the compiler deduce types automatically, and only provide type annotations when necessary. With reliance on type inferences developers can write more concise and maintainable TypeScript code, but this reliance can also introduce some unexpected behaviors that over-annotation could have caught.

## 2.11. Summary

- We shouldn't use any too often to increase the benefits of TypeScript

- We shouldn't ignore TypeScript compiler warnings

- We should use strict mode to catch more errors

- We should correctly declare variables with let and const

- We should use optional chaining ? when we need to check for existence of a property

- We should use nullish coalescing to check ?? for null and undefined, instead of ||

- We should export and import modules properly using ES6 modules notation.

- We should understand type assertions and not over rely on unknown

- We should use === in places of == to ensure proper checks.

- We should understand the type inference capabilities and over-annotate if feasible.


