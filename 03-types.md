

# 3. Types, Aliases and Interfaces

This chapter covers

- Understanding the difference between type aliases and interfaces

- Putting into practice type widening

- Ordering type properties and extending interfaces suitably

- Applying type guards appropriately

- Making sense of the readonly property modifier

- Utilizing the keyof and Extract utility types effectively

Getting to grips with TypeScript can feel a bit like being invited to an exclusive party where everyone is speaking a slightly different dialect of a language you thought you knew well. In this case, the language is JavaScript, and the dialect is TypeScript. Now, imagine walking into this party and hearing words like "types", "type aliases" and "interfaces" being thrown around. It might initially sound as though everyone is discussing an unusual art exhibition! But, once you get the hang of it, these terms will become as familiar as your favorite punchline.

Among the array of unique conversations at this TypeScript soirée, you'll find folks passionately debating the merits and shortcomings of type widening, type guards, type aliases and interfaces. These TypeScript enthusiasts could put political pundits to shame with their fervor for these constructs. To them, the intricate differences between TypeScript features are not just programming concerns---they're a way of life. And if you've ever felt that a codebase without properly defined types is like a joke without a punchline, well, you're in good company. Speaking of jokes: Why did the TypeScript interface go to therapy? --- Because it had too many unresolved properties!

But don't worry. This chapter will guide you through the bustling crowd at the TypeScript party, ensuring you know just when to be the life of the party and when to responsibly drive your codebase home. After all, in TypeScript as in comedy, timing is everything. We're going to deep dive into these tasty TypeScript treats, learning when each one shines and how to use them without causing a stomach problem. Along the way, we'll learn to avoid some of the most common pitfalls like type widening, readonly, keyof, type guards, type mapping, type aliases and others that can leave your codebase looking like a pastry after kindergarteners. And while we are on the dessert theme, I can't withhold another joke: A TypeScript variable worried about gaining weight, because after all those desserts it didn't want to become a _Fat Arrow Function_!

So, get ready to embark on this exploration of types and interfaces. By the end of this chapter, you should be able to discern between these two, just like telling apart your Aunt Bertha from your Aunt Gertrude at a family reunion -- it's all in the details. And remember, if coding was easy, everybody would do it. But if everyone did it, who would we make fun of for not understanding recursion? Let's dive in!

## 3.1. Confusing Types Aliases and Interfaces

In the TypeScript world, type aliases and interfaces can be thought of as two sides of the same coin---or more aptly, two characters in a comedic duo. One might be more flexible, doing all sorts of wild and unpredictable things (hello, types), while the other is more reliable and consistent, providing a predictable structure and ensuring that everything goes according to plan (that's you, interfaces). But like any good comedy duo, they both have their strengths and weaknesses, and knowing when to utilize each is key to writing a script---or in this case, code---that gets the biggest laughs (or at least, the least number of bugs).

Types in TypeScript are like the chameleons of the coding world. They can adapt and change to fit a variety of situations. They're versatile, ready to shape-shift into whatever form your data requires. And yet, they have their limitations. Imagine a chameleon trying to blend into a Jackson Pollock painting - it's going to have a tough time! So, while types are handy, trying to use them for complex or changing structures can lead to messy code faster than you can say "type confusion".

On the other hand, we have interfaces. If type aliases are chameleons, interfaces are more like blueprints for a house or piece of furniture. They give you a concrete structure, a detailed plan to follow, ensuring that your objects are built to spec. However, like a blueprint, if your construction deviates from the plan, you're going to end up with compiler errors that look more frightening than your unfinished IKEA furniture assembly. And let's face it, nobody likes to be halfway through a project only to find out they're missing a 'semicolon' or two!

Remember, TypeScript compiles down to JavaScript, so ultimately both of these constructs are just tools to provide stronger type safety and autocompletion in your compilers and the editor.

Type Aliases: The type keyword in TypeScript is used to define custom types, which can include _aliases_ for existing types, union types, intersection types, and more. Types can represent any kind of value, including primitives, objects, and functions. type creates an alias to refer to a type. The following example defines two types, and object variables and a class that use the types:

```typescript
type Coordinate = number | string;      //  #A
const latitude: Coordinate = "30°47′41.841″ N";
const longitude: Coordinate = -122.276582;

type Point = {                          //  #B
  x: number;
  y: number;
};

let point: Point = {
  x: 10,
  y: 20,
};

class Circle implements Point {         //  #C

  x: number = 0;
  y: number = 0;
  radius: number = 10;
}
```

`#A Type alias for the union type.`
`#B Type alias for the object type with x, y coordinates.`
`#C Type aliased is used to define the required properties of the class`

Please note that when we implement a type, we can add more properties like radius in the preceding example and at the same time we must provide all the properties of the type that we implement (Point).

Interfaces: The interface keyword is used to define a contract for objects, describing their shape and behavior. Interfaces can be implemented by classes, extended by other interfaces, and used to type-check objects. They cannot represent primitive values or union types.

The following example defines an interface Shape, an object that is of the type Shape, and then a class that implements this interface:

```typescript
interface Shape {
  area(): number;
}

let shape100: Shape = {               //  #A
  area: () => {
    return 100;
  },
};

class Circle implements Shape {       //  #B
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  area(): number {
    return Math.PI * this.radius ** 2;
  }
}
```

`#A Object shape must be the same shape as the interface Shape.`
`#B Class Circle must have properties overlap with the Shape interface, i.e., method area().`

By the way, in some TypeScript code outside of this book, you may see interfaces postfixed (ends) with I letter as in ShapeI. The motivation here is clear---to differentiate between class or type alias. However, this notation is discouraged by TS professionals as can be seen in this GitHub discussion: <https://github.com/microsoft/TypeScript-Handbook/issues/121>. In my opinion this notation is unnecessary.

To demonstrate the similarities between type aliases and interfaces, let's see how we can rewrite our example that used type aliases with interfaces instead. We need to replace equal signs with curly braces, keywords type with interface and because we cannot define union type with interface, we must create a workaround value property, as follows:

```typescript
interface Coordinate {       //  #A
  value: number | string;
}

const latitude: Coordinate = {
  value: "30°47′41.841″ N",
};

const longitude: Coordinate = {
  value: -122.276582,
};

interface Point {           //  #B
  x: number;
  y: number;
}

let point: Point = {
  x: 10,
  y: 20,
};

class Circle implements Point {
  x: number = 0;
  y: number = 0;
  radius: number = 10;
}
```

`#A Union type needs to become an object type with the value property.`
`#B We don't use an equal sign = when defining interfaces unlike with type aliases.`

While in the previous example type aliases and interfaces were interchangeable, here's a cool trick that interfaces can do (and type aliases cannot). Interfaces can be "re-opened". The technical term is declaration merging. This is TypeScript's approach to representing methods introduced in different ECMAScript versions for built-i types, such as Array. The following example illustrates how interfaces can be "re-opened":

```typescript
interface User {
  name: string;
}

interface User {
  age: number;          //  #A
}

let user: User = {
  name: "Petya",
  age: 18,              //  #B
};

type Point = {
  x: number;
};

type Point = {          //  #C
  y: number;
};
```

`#A This is perfectly fine; the User interface now has a name and an age.`
`#B Our object literal has the "added" property age.`
`#C This will raise a Duplicate identifier error.`
_The_ main difference between type aliases and interfaces is that type aliases are more flexible in that they can represent primitive types, union types, intersection types, etc., while interfaces are more suited for object type checking and class and object literal expressiveness (really all they can do!). And as we saw, type aliases cannot be "re-opened" to add new properties vs interfaces which are always extendable.

Here's a short list of when to use type aliases vs. interfaces:

- Use interfaces for object shapes and class contracts: Interfaces are ideal for defining the shape of an object or the contract a class must implement. They provide a clear and concise way to express relationships between classes and objects.

- Use types for more complex and flexible structures: Type aliases are more versatile and can represent complex structures, such as union types, intersection types, and mapped types (operation on types that produce an object type, not a type in and of themselves per se), tuple types or literal types, and function types (although they can be defined with an interface too). Ergo, use types when you need more flexibility and complexity in your type definitions.

- Interfaces can "extend" / "inherit" from other types (interfaces and type aliases) but type aliases cannot (but they can use an intersection &)

- Interfaces support declaration merging while type aliases do not.

- Type aliases can define types that cannot be represented as an interface, such as union types, tuple types, and other complex or computed types.

I> Sidenote and a power tip: combining types and interfaces when necessary. In some cases, it may be beneficial to combine types and interfaces to create more powerful and expressive type definitions. For example, you can use a type to represent a union of multiple interfaces or extend an interface with a type. Here's an example in which we'll define a few interfaces and then combine them with types to create a union type that can represent multiple different shapes of data.

Let's define some interfaces for different kinds of pets:

```typescript
interface Dog {
  species: "dog";
  bark: () => void;
}

interface Cat {
  species: "cat";
  purr: () => void;
}

interface Bird {
  species: "bird";
  sing: () => void;
}
```

Next, let's use a type to represent a union of the interfaces:

```typescript
type Pet = Dog | Cat | Bird;
```

An example function that takes a Pet type. Inside, we can use type narrowing to interact with the pet based on its species

```typescript
function interactWithPet(pet: Pet) {
  console.log(`Interacting with a ${pet.species}.`);
  if (pet.species === "dog") {
    pet.bark();
  } else if (pet.species === "cat") {
    pet.purr();
  } else if (pet.species === "bird") {
    pet.sing();
  }
}
```

Another example using the intersection type with an additional property:

```typescript
type PetWithID = Pet & { id: string };
```

Creating an object that satisfies PetWithID:

```typescript
const myPet: PetWithID = {
  species: "dog",
  bark: () => console.log("Woof!"),
  id: "D123",
};
```

Using the function is straightforward:

```typescript
interactWithPet(myPet);
console.log(`Pet ID is ${myPet.id}.`);
```

In this example:

- We've defined three interfaces: Dog, Cat, and Bird, each representing different kinds of pets with unique behaviors.

- We then create a Pet type that can be either a Dog, Cat, or Bird. This is the union type, allowing us to define a variable that can hold multiple shapes of data.

- We define a function interactWithPet that accepts a Pet type and uses type narrowing to call the appropriate method based on the pet's species.

- We extend an interface (Pet) with additional properties (ownerName) to create a new interface (PetWithOwner).

- We also create an intersection type (PetWithID) that combines our Pet type with an additional id property. This is useful for cases where a pet needs to have a unique identifier.

By combining interfaces and types, you can create complex and flexible type definitions that can accommodate various scenarios in a TypeScript application.

Now, you may be still asking yourself, "Which should I use? Type aliases or interfaces?". If you ask my opinion and as a meaning of proving you with a mental shortcut, I recommend starting with using interfaces until or unless you need more flexibility that the types can provide. This way by defaulting to interfaces, you'll get the type safety and remove an extra cognitive load of constantly thinking of what should be used here: type or interface.

Next, let's see the most common pitfalls to avoid when working with types and interfaces in TypeScript:

- Mixing up types and interfaces approaches: Be aware of the differences between types and interfaces and choose the appropriate one for your use case. Once you pick the approach (e.g., using interfaces for declaring a shape of an object), follow it for consistency in a given project.

- Overusing union types in interfaces: While it's possible to use union types within an interface, overusing them can make the interface harder to understand and maintain. Consider refactoring complex union types into separate interfaces or using types for more complex structures. More on this we'll cover in _3.7. Overcomplicating Types._

Let's step aside from type aliases and interfaces and touch on a different but important topic of types vs. values. A common error developers might encounter when working with TypeScript is "_only refers to a type but is being used as a value here._" This error occurs when a type or an interface is used in a context where a value is expected. Since types and interfaces are only used for compile-time type checking and do not have a runtime representation, they cannot be treated as values. To resolve this error, ensure that you are using the correct construct for the context. If you need a runtime value, consider using a class, enum, or constant instead of a type or interface. Understanding the distinction between types and values in TypeScript is crucial for avoiding this error and writing correct, maintainable code.

Here is a code example illustrating the aforementioned error: "only refers to a type, but is being used as a value here." This will cause the error: "MyType only refers to a type, but is being used as a value here.":

```typescript
type MyType = {
  property: string;
};

const instance = new MyType(); //  #A
interface MyTypeI {            //  #B
  property: string;
}

const instance = new MyType(); // #C
```

`#A Incorrect usage: trying to use a type as a value`
`#B Same error with interfaces`
`#C Erro`


Solution: use a class, enum, or constant instead of a type.

```typescript
class MyClass implements MyType {
  property: string;
  constructor(property: string) {
    this.property = property;
  }
}

const instance = new MyClass("Hello, TypeScript!"); //  #A
console.log(instance.property);                     //  #B

class MyClass implements MyTypeI {
  property: string;
  constructor(property: string) {
    this.property = property;
  }
}

const instance = new MyClass("Hello, TypeScript!"); //  #C
console.log(instance2.property);
```

`#A Correct usage: instantiating a class`
`#B Hello, TypeScript!`
`#C Correct usage: instantiating a class`

In this preceding example, attempting to instantiate MyType as if it were a class causes the error. To resolve it, we define a class MyClass with the same structure as MyType and instantiate MyClass instead. This demonstrates the importance of understanding the distinction between types and values in TypeScript and using the correct constructs for different contexts.

All in all, by understanding the differences between type aliases and interfaces, developers can choose the right construct for their use case and write cleaner, more maintainable TypeScript code. Type Aliases: Best suited for defining unions, intersections, tuples, or when you need to apply complex type transformations. Interfaces: Ideal for declaring shapes of objects and classes, especially when you anticipate extending these types through declaration merging. Consider the strengths and weaknesses of each construct and use them in combination when necessary to create powerful and expressive type definitions.

## 3.2. Misconceiving Type Widening

Type widening is a TypeScript concept that refers to the automatic expansion of a type based on the context in which it is used. This process, a.k.a., inference, can be helpful in some cases, but it can also lead to unexpected type issues if not properly understood and managed. This section will discuss the concept of type widening, its implications, and best practices for working with it effectively in your TypeScript code.

Let's begin with understanding the type widening. Type widening occurs when TypeScript assigns a broader type to a value based on the value's usage or context. This often happens when a variable is initialized with a specific value, and TypeScript widens the type to include other potential values.

Example of a string variable with a specific value but widened type:

```typescript
let message = "Hello, TypeScript!"; //  #A
```

`#A Widened type: string`

In this example, the message variable is initialized with a string value. TypeScript automatically widens the type of message to string, even though the initial value is a specific string literal.

Type widening can also result in unintended type assignments, as TypeScript may widen a type more than necessary, causing potential type mismatches. Thus, the best practices for working with type widening: explicit type annotation and const.

- Use explicit type annotations: To prevent unintended type widening, you can use explicit type annotations to specify the exact type you want for a variable or function parameter.

```typescript
let message: "Hello, TypeScript!" = "Hello, TypeScript!"; //  #A
```

`#A Type: "Hello, TypeScript!"`

In this example, by providing an explicit type annotation, we prevent TypeScript from widening the type of message to string, ensuring that it remains the specific string literal type.

- Use const for immutable values: When declaring a variable with an immutable value, use the const keyword instead of let. This will prevent type widening for primitives (not objects), as const variables cannot be reassigned.

```typescript
const message = "Hello, TypeScript!"; //  #A
```

`#A Type: "Hello, TypeScript!"`

We can also use as const on non-const variables. In the following example, re-assigning the value of the apiUrl variable will show an error: Type '"https://malicious-website.com/api"' is not assignable to type '"https://azat.co/api/v1"'. apiUrl is treated as a literal type with the value https://azat.co/api/v1, not just a type string.

```typescript
let apiUrl = "https://azat.co/api/v1" as const;
apiUrl = "https://malicious-website.com/api";
```

This is useful for configurations and such. We can also use as const with const but const is already preventing changes by itself.

Here's another example illustrating TypeScript type widening in action:

```typescript
function displayText(text: string) {
  console.log(text);
}

const greeting = "Hello, TypeScript!";      //  #A
displayText(greeting);                      //  #B
const specificGreeting: "Hello, TypeScript!" = "Hello, TypeScript!"; //  #C
displayText(specificGreeting);              //  #D
```

`#A Widened type: string`
`#B No error, as the type of greetingis widened to string`
`#C Preventing type widening using explicit type annotation`
`#D Error: Argument of type '"Hello, TypeScript!"' is not assignable to parameter of type 'string'.`

In this example, we define a displayText function that takes a text parameter of type string. When we declare the greeting variable without an explicit type annotation, TypeScript automatically widens its type to string, allowing it to be passed as an argument to the displayText function without any issues.

Then, we declare the specificGreeting variable with an explicit type annotation of "Hello, TypeScript!" string literal, and TypeScript does not widen the type. As a result, passing specificGreeting to the displayText function will NOT raise a type error, since "Hello, TypeScript!" string literal is assignable to the more general string type expected by the function (text parameter).

Here's an example illustrating the unintentional reliance on TypeScript type widening:

```typescript
function getPetInfo(pet: { 
    species: string; 
    age: number }) {
  return `My ${pet.species} is ${pet.age} years old.`;
}

const specificDog = { species: "dog", age: 3 };         //  #A
const dogInfo = getPetInfo(specificDog);                //  #B
specificDog.species = "cat";                            //  #C
const updatedDogInfo = getPetInfo(specificDog);         //  #D
```

`#A Widened type: { species: string; age: number; }`
`#B No error, as the type of specificDog is widened to { species: string; age: number; }`
`#C Later in the code, a developer mistakenly updates the object`
`#D The specificDog object is no longer accurate, but the type system does not catch this error due to type widening; Returns: "My cat is 3 years old."`

In this example, we define a getPetInfo function that takes a pet parameter with a specific shape. When we declare the specificDog variable without an explicit type annotation, TypeScript automatically widens its type to { species: string; age: number; }, allowing it to be passed as an argument to the getPetInfo function without any issues.

However, later in the code, a developer mistakenly updates the specificDog.species property to "cat". Due to type widening, TypeScript does not catch this error, and the getPetInfo function returns an inaccurate result. This demonstrates how unintentionally relying on type widening can make the code less maintainable and more prone to errors.

To prevent such issues, consider using explicit type annotations or creating a type alias (or interface) to represent the expected object shape:

```typescript
type Dog = {
  species: "dog";
  age: number;
};

const specificDog: Dog = { 
  species: "dog", 
  age: 3 
};
specificDog.species = "cat";        //  #A
specificDog satisfies Dog;          //  #B
```

`#A Error: Type '"cat"' is not assignable to type '"dog"'`
`#B Yes, it satisfies. No errors here.`

Note: We can achieve the same by using an interface Dog or even an inline definition as follows:
```typescript
const specificDog: { 
  species: 'dog', age: number 
} = { 
  species: 'dog', 
  age: 3 
};
```

The satisfies keyword is a useful tool in TypeScript for ensuring type safety and compatibility in a way that maintains the integrity and original structure of your types. It's especially beneficial in complex codebases where strict type conformance is crucial without sacrificing the flexibility of the types.

Now, what do you think will happen if we remove the explicit type annotation from the variable specificDog, but leave it in the function argument pet? That is what if we have code like this:

```typescript
type Dog = {
  species: "dog";
  age: number;
};

function getPetInfo(pet: Dog) {
  return `My ${pet.species} is ${pet.age} years old.`;
}

const specificDog = { species: "dog", age: 3 };
const dogInfo = getPetInfo(specificDog);      //  #A
specificDog satisfies Dog;                    //  #B
specificDog.species = "cat";                  //  #C
```

`#A Argument of type '{ species: string; age: number; }' is not assignable to parameter of type 'Dog'.`
`#B Type '{ species: string; age: number; }' does not satisfy the expected type 'Dog'`
`#C Okay, no TS errors which can lead to bugs.`

Surprise! Widening went too far by making the specificDog property species a string, which in turn made our object specificDog incompatible with the pet of type Dog function parameter.

By using an explicit type annotation (with an interface or a type alias), you can avoid overlooking type widening and ensure that your code remains accurate and maintainable. On the other hand, widening could cause a type error if it widens too far. To sum up, the most common pitfalls to avoid when dealing with type widening in TypeScript are:

- Overlooking type widening: Be aware of when and where type widening may occur in your code, as overlooking it can lead to unexpected behavior or type-related errors.

- Relying on type widening unintentionally: While type widening can be helpful in certain situations, relying on it unintentionally can make your code less maintainable and more prone to errors. Be intentional in your use of type widening, and use explicit type annotations when necessary.

- Enjoy while type widening works as intended. It's a good addition to the language (when developers know how it works).

By understanding the concept of type widening and its implications, developers can write more robust and maintainable TypeScript code. Be mindful of when and where type widening may occur, and use explicit type annotations and the const keyword to prevent unintended widening. This will result in a more precise and reliable type system, helping to catch potential errors at compile time.

## 3.3. Ordering Type Properties Inconsistently

Inconsistent property ordering in interfaces and classes can lead to code that is difficult to read and maintain. Ensuring a consistent order of properties makes the code more predictable and easier to understand. Let's look at some examples to illustrate the benefits of consistent property ordering.

In the following example, the properties are ordered inconsistently with interface and class declarations: name, age, address, jobTitle:

```typescript
interface InconsistentPerson {
  age: number;
  name: string;
  address: string;
  jobTitle: string;
}

class InconsistentEmployee implements InconsistentPerson {
  address: string;
  age: number;
  name: string;
  jobTitle: string;

  constructor(
    name: string,
    age: number,
    address: string,
    jobTitle: string
  ) {
    this.name = name;
    this.age = age;
    this.address = address;
    this.jobTitle = jobTitle;
  }
}

const employee = new InconsistentEmployee(
  "Anastasia",
  30,
  "123 Main St.",
  "Software Engineer"
);

console.log(employee);
```

In this example, the InconsistentPerson interface and object of the InconsistentEmployee class (employee) have their properties ordered inconsistently. This makes the code harder to read, as developers must spend more time searching for the properties they need. It's easy to make a mistake in the new InconsistentEmployee() constructor call.

Now, let's see an example with consistent property ordering:

```typescript
interface ConsistentPerson {
  name: string;
  age: number;
  address: string;
  jobTitle: string;
}

class ConsistentEmployee implements ConsistentPerson {
  name: string;
  age: number;
  address: string;
  jobTitle: string;

  constructor(
    name: string,
    age: number,
    address: string,
    jobTitle: string
  ) {
    this.name = name;
    this.age = age;
    this.address = address;
    this.jobTitle = jobTitle;
  }
}

const employee = new ConsistentEmployee(
  "Pavel",
  25,
  "456 Main Ave.",
  "Product Manager"
);

console.log(employee);
```

Alternatively, we can replace multiple constructor parameters with an object (with a defined custom type). This will also help not to mess up the order of the parameters. But using a single object instead of several arguments is a whole new pattern with its pros and cons which we'll leave to others to debate.

By consistently ordering properties in interfaces and classes, we make the code more predictable and easier to read. This can lead to improved productivity and maintainability, as developers can quickly find and understand the properties they need to work with.

In conclusion, maintaining a consistent order of properties in your TypeScript code is essential for readability and maintainability. By following a predictable pattern, developers can better understand and navigate the code, resulting in a more efficient and enjoyable development experience.

## 3.4. Extending Interfaces Unnecessarily

In TypeScript, extending interfaces can help you create more complex and reusable types. However, unnecessary interface extension can lead to overcomplicated code and hinder maintainability. In this chapter, we'll discuss the issues that can arise from unnecessary interface extension and explore ways to simplify the code.

Consider this example of interfaces Mammal, Dog and Animal:

```typescript
interface Animal {                 //  #A
  name: string;
  age: number;
}

interface Mammal extends Animal {} //  #B
interface Dog extends Mammal {}    //  #C

const myDog: Dog = {               //  #D
  name: "Buddy",
  age: 3,
};

console.log(myDog);                //  #E
```

`#A Base interface`
`#B Extended interface with no additional properties`
`#C Another extended interface with no additional properties`
`#D Usage of the extended interface`
`#E The Dog and Mammal interfaces add no additional value.`

The base interface was extended to include the Dog and Mammal interfaces, but with no additional properties. This means that the new interfaces bring no additional value. They just add unnecessary bloat to the code. We can simplify the preceding version by removing empty interfaces Dog and Mammal:

```typescript
interface SimplifiedAnimal {
  name: string;
  age: number;
}

const mySimplifiedDog: SimplifiedAnimal = {
  name: "Buddy",
  age: 3,
};

console.log(mySimplifiedDog);
```

The SimplifiedAnimal interface is more concise and easier to understand.

Here's another example with an empty interface Manager:

```typescript
interface Person {                    //  #A
  name: string;
  age: number;
}

interface Employee extends Person {   //  #B
  title: string;
  department: string;
}

interface Manager extends Employee {} //  #C

const myManager: Manager = {          //  #D
  name: "Anastasia",
  age: 35,
  title: "Project Manager",
  department: "IT",
};

console.log(myManager);
```

`#A Base interface`
`#B Extended interface with unrelated properties`
`#C Another extended interface with no additional properties`
`#D Usage of the extended interface`

The Manager interface adds no additional value, because the body of interface Manager is empty. There are no properties Thus, we can simplify our code base. It's worth mentioning that there's an ESLint rule to ban empty interfaces: no-empty-interface.

We can keep Person and Employee (more specific person with properties specific to an employee) or just simplify into a single interface (unless Person is used elsewhere in the code):

```typescript
interface SimplifiedEmployee {
  name: string;
  age: number;
  title: string;
  department: string;
}

const mySimplifiedManager: SimplifiedEmployee = {
  name: "Anastasia",
  age: 35,
  title: "Project Manager",
  department: "IT",
};

console.log(mySimplifiedManager);
```

The final SimplifiedEmployee interface is more concise and easier to understand than the initial code. It doesn't have an empty interface. Of course, you may be thinking, "Hey, I'll need that empty interface in the future" and this can be true. However, right now the code has become more complex. The same principle of simplicity applies to not just empty interfaces but to interfaces that can be combined or merged into other interfaces.

By looking at our example, you may think that extending interfaces is always a bad idea but that's not true. Extending interfaces in TypeScript isn't inherently bad; it's a powerful feature that allows for more flexible and reusable code. However, whether or not it's advisable depends on the context and how it's used. For example, extending interfaces is particularly good and useful when we have a set of objects that share common properties but also have their own unique properties.

Imagine we are creating a user management system where we need to handle different types of users: Admin, Member and Guest. All users share common properties id, name and email, but they also have unique properties and methods. We define a base interface User and then extend it with unique properties for each child interface:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}
```

For Admin users:

```typescript
interface Admin extends User {
  adminLevel: string;
  createPost(content: string): void;
}
```

For Member users:

```typescript
interface Member extends User {
  membershipType: string;
  renewMembership(): void;
}
```

For Guest users:

```typescript
interface Guest extends User {
  expirationDate: Date;
}
```

Now, we can use these interfaces to define functions or classes that work with specific user types. For example, we can create a function that take Admin as an argument and invoke it:

```typescript
function createAdmin(user: Admin) {      // #A  
  console.log(`Creating admin user: ${user.name}`);
}

const adminUser: Admin = {              // #B
  id: 1,
  name: "Alisa",
  email: "alisa@qq.com",
  adminLevel: "super",
  createPost: (content: string) => console.log(`Posting: ${content}`),
};

createAdmin(adminUser);                 // #C

adminUser.createPost(                   // #D
  "10 Reasons TypeScript is the 'Type' of Friend JavaScript Didn't Know It Needed!"
);          
```

`#A Logic specific to creating an admin user`
`#B Sample admin user`
`#C Creating admin user: Alisa`
`#D Works perfectly!`

In this users types example, extending interfaces organizes the code, making it scalable and maintainable, which is especially beneficial in larger or more complex TypeScript applications.

In conclusion, it's essential to avoid _unnecessary_ interface extension in your TypeScript code. By keeping your interfaces concise and focused, you can improve code readability and maintainability. Always consider whether extending an interface adds value or complexity to your code and opt for simplicity whenever possible.

## 3.5. Missing on Opportunities to Use Type Aliases

Type aliases are a useful feature in TypeScript, allowing you to create a new name for a type, making your code more readable and maintainable. A type alias is, in essence, an assigned name given to any specific type. Ignoring type aliases can lead to code duplication, reduced readability, and increased maintenance effort. In this section, we will discuss the importance of type aliases and provide guidance on how to use them effectively. Please note that in this section when I talk about type aliases, in many cases interface can be a substitute for type alias, because interface defines a type.

Repeating complex types throughout your codebase can lead to duplication and make your code harder to maintain. Type aliases help you avoid this problem by providing a single point of reference for a type. In the following example, we don't use type aliases and end up with code duplication:

```typescript
function processText(text: string | null | undefined): string {
  // ...do something
  return text ?? "";
}

function displayText(text: string | null | undefined): void {
  console.log(text ?? "");
}

function customTrim(text: string | null | undefined): string {
  if (text === null || text === undefined) {
    return "";
  }

  let startIndex = 0;
  let endIndex = text.length - 1;

  while (startIndex < endIndex && text[startIndex] === " ") {
    startIndex++;
  }

  while (endIndex >= startIndex && text[endIndex] === " ") {
    endIndex--;
  }

  return text.substring(startIndex, endIndex + 1);
}
```

In the example above, the complex type string | null | undefined is repeated in both function signatures. Using a type alias (NullableString ) can simplify the code:

```typescript
type NullableString = string | null | undefined;

function processText(text: NullableString): string {
  return text ?? "";
}

function displayText(text: NullableString): void {
  console.log(text ?? "");
}

function customTrim(text: NullableString): void {
  // ...
}
```

Using type aliases can make your code more readable by providing descriptive names for complex types or commonly used type combinations. For example, consider this code without type alias that has two functions that take exactly the same argument dimensions:

```typescript
function rectangleArea(dimensions: { width: number; height: number }): number {
  return dimensions.width * dimensions.height;
}

function rectanglePerimiter(dimensions: {
  width: number;
  height: number;
}): number {
  return (dimensions.width + dimensions.height) * 2;
}

console.log(rectangleArea({ width: 10, height: 4 }));       // 40
console.log(rectanglePerimiter({ width: 10, height: 4 }));  // 28
```

As a next step, let's add a type alias RectangleDimensions to improve the readability (and avoid code duplication), especially if we have to use RectangleDimensions over and over again in many places and not just these two functions. Outlined below is how it looks with the type alias:

```typescript
type RectangleDimensions = { width: number; height: number };

function rectangleArea(dimensions: RectangleDimensions): number {
  // ...
}

function rectanglePerimiter(dimensions: RectangleDimensions): number {
  // ...
}
```

In the example above, using a type alias for RectangleDimensions improves the readability of the rectangleArea and rectanglePerimiter functions signature.

Type aliases can also help encapsulate type-related logic, making it easier to update and maintain your code (as well as improve readability and avoid code duplication).

Consider this code with a union type alias ApiResponse that has API response structure for a successful response and a failed (error) response:

```typescript
type ApiResponse<T> =
  | { data: T; status: number }
  | { status: number; error: string };
```

Successful response will have data of the T type and status but no error, while the failed response would have error and status fields but no data.

Proceeding, we can create separate type aliases for success and error. This will allow us to use response types elsewhere and make the code more readable. After that, we can still create a union type for the ApiResponse type:

```typescript
type SuccessResponse<T> = { data: T; status: number };
type ErrorResponse = { status: number; error: string };
type ApiResponse<T> = SuccessResponse<T> | ErrorResponse;
```

In the example above, using type aliases for SuccessResponse and ErrorResponse makes the union type ApiResponse easier to understand and maintain. ApiResponse<T> type represents any API response. It's a union type, so an ApiResponse can be either a SuccessResponse or an ErrorResponse. T is again a placeholder for the type of data in the SuccessResponse. If you have an API endpoint that returns a User, you might use these types like this:

```typescript
type SuccessResponse<T> = { data: T; status: number};
type ErrorResponse = { status: number, error: string };
type ApiResponse<T> = SuccessResponse<T> | ErrorResponse;

type User = {
  id: string;
  name: string;
}

function getUser(id: User['id']): ApiResponse<User> {
  if (Math.random()<0.5) {         //  #A
    return {
      data: {
        id: '123',
        name: 'Petya'
      },
      status: 200
    }
  } else {
    return {
      status: 500,
      error: "500 server error"
    }
  }
}

console.log(getUser('123'))        //  #B
```

`#A Random function that either returns success or error`
`#B We invoke the getUser with user ID`

In this case, getUser is a function that returns an ApiResponse<User>. This means the returned object could either be a SuccessResponse<User> (with data being a User object and error none existing), or an ErrorResponse (with data none existing and error being a strin).

Keep in mind that aliases simply serve as alternative names and do not create unique or distinct "versions" of the same type. When employing a type alias, it functions precisely as if you had written the original type it represents. And of course, all type information will be stripped during the compilation so the JavaScript code would not have any notion of SuccessResponse nor ErrorResponse.

In conclusion, type aliases are an essential tool in TypeScript for promoting code readability and maintainability. Avoid ignoring type aliases in favor of duplicating complex types or using less descriptive type combinations. By using type aliases effectively, you can create cleaner, more maintainable TypeScript code.

## 3.6. Avoiding Type Guards

Type guards are a powerful feature in TypeScript that allows you to narrow down the type of a variable within a specific block of code. Failing to use type guards can lead to code that is less safe, and more prone to errors. In this chapter, we will discuss the importance of type guards and show examples of how to use them effectively.

Next, we have an example of two interfaces, union of them and a function that takes the union parameter to provide area of the shape. In this example we don't use type guards, nor assertions, nor check the tag on a tagged union shape. (More on type guards and tags later.) This leads us to errors shown below:

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape, shapeType: string): number {
  if (shapeType === "circle") {
    return Math.PI * shape.radius ** 2;   //  #A
  } else {
    return shape.sideLength ** 2;         //  #B
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle, "circle")); // 78.53981633974483
```

`#A Error: Property 'radius' does not exist on type 'Shape'. Property 'radius' does not exist on type 'Square'.`
`#B Error: Property 'sideLength' does not exist on type 'Shape'. Property 'sideLength' does not exist on type 'Circle'.`

In the preceding example, we have a Circle and a Square interface, both belonging to the Shape type. The getArea function calculates the area of a shape, but it doesn't use type guards nor tagging nor assertion. In other words, TypeScript is confusd.

To fix the errors, one of the solutions is to use type assertions (shape as Circle and shape as Square) to access the specific properties of each shape. The type assertions in TypeScript are a way to tell the compiler _"trust me, I know what I'm doing."_ Here's how we can fix the errors with type assertions:

```typescript
function getArea(shape: Shape, shapeType: string): number {
  if (shapeType === "circle") {
    return Math.PI * (shape as Circle).radius ** 2;
  } else {
    return (shape as Square).sideLength ** 2;
  }
}
```

What if we can use a user-defined type guard (isCircle) instead of relying on assertions (as)? Here's a changed code example in which we introduce isCircle that returns a boolean. We can also leverage the type property of the Circle and Square types:

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function isCircle(shape: Shape): shape is Circle {  //  #A
  return shape.type === "circle";
}

function getArea(shape: Shape): number {
  if (isCircle(shape)) {
    return Math.PI * shape.radius ** 2;
  } else {
    return shape.sideLength ** 2;
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle));
```

`#A Defining a type guard (isCircle) to narrow the type of the shape`

In this example, we've introduced a type guard function called isCircle, which narrows the type of the shape within the if block. This makes the code safer and more efficient, as we no longer need to use type assertions to access the specific properties of each shape. TypeScript can figure out based on the if/else structure what type it is dealing with, Circle or Square. However, in this code it is easy to introduce a bug. Let's say someone unintentionally changes the body of isCircle to wrongly compare it with a square type. Then we won't see any TypeScript errors but we will have a run-time bug leading to the wrong result:

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function isCircle(shape: Shape): shape is Circle {
  return shape.type === "square";                 //  #A
}

function getArea(shape: Shape): number {
  if (isCircle(shape)) {
    return Math.PI * shape.radius ** 2;           //  #A
  } else {
    return shape.sideLength ** 2;                 //  #B
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle));                   //  #C
```

`#A Wrong code that is easy to miss`
`#B No TS error`
`#C Run-time error: NaN`

Interestingly, if we remove the isCircle completely and do the type guard check right in the function getArea, then TypeScript is smart enough to catch inconsistency between having true for the === square check and trying to access the radius property:

```typescript
function getArea(shape: Shape): number {
  if (shape.type === "square") {
    return Math.PI * shape.radius ** 2;           //  #A
  } else {
    return shape.sideLength ** 2;                 //  #B
  }
}
```

`#A Correctly shows the error: Property 'radius' does not exist on type 'Square'.`
`#B Also correctly shows the error; Property 'sideLength' does not exist on type 'Circle'.`

As far as type guards go, when we provide a user-defined type guard (e.g., separate function isCircle), we take away the TypeScript built-in control flow analysis. It's better not to do it unless necessary (e.g., code reus).

Let's carry on with the preceding code that has a bug in it, because the logic is reversed (type square returns area of a circle). To fix it, we must return to the correct if check shape.type === circle or switch area statements. This is the code with the ideal approach (using type guards directly) that has no TS errors, and makes it harder to introduce run-time errors by showing problematic areas:

```typescript
interface Circle {
  type: "circle";
  radius: number;
}

interface Square {
  type: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function getArea(shape: Shape): number {
  if (shape.type === "circle") {
    return Math.PI * shape.radius ** 2;
  } else {
    return shape.sideLength ** 2;
  }
}

const myCircle: Circle = { type: "circle", radius: 5 };
console.log(getArea(myCircle));         // ~78.54

const mySquare: Square = { type: "square", sideLength: 5 };
console.log(getArea(mySquare));         // 25
```

In the given example, we use a type property on the object, because the typeof operator is not suitable for discriminating object union members. This is because the typeof operator cannot differentiate between object types like classes and constructor functions. It will always return object or function. It is only useful to check the primitive types (number, string, boolean) and not objects.

Subsequently, we'll see an example using typeof for primitives in a type guard directly in a function. Here's a new example with primitive types number and string that are randomly passed to the describeType function:

```typescript
type PrimitiveType = string | number;

function describeType(value: PrimitiveType): string {
  if (typeof value === "number") {
    return `The number is ${value.toFixed(2)}`;         //  #A
  } else {
    return `The string is "${value.toUpperCase()}"`;    //  #B
  }
}

const numOrStr: PrimitiveType = Math.random() > 0.5 ? 42 : "hello";
console.log(describeType(numOrStr)); //  #C

const numOrStr2: PrimitiveType = Math.random() > 0.5 ? 42 : "hello";
console.log(describeType(numOrStr2));
```

`#A TypeScript knows that 'value' is a number within this block, so no TS errors`
`#B TypeScript knows that 'value' is a string within this block, so no TS errors`
`#C Output could randomly be either number 42 or a string hello`

In this example, we use a type guard function isNumber that checks if the value is a number using the typeof operator. The describeType function then uses this type guard to distinguish between number and string values and provide a description accordingly.

Although it may appear unassuming, there is actually a significant amount of activity happening beneath the surface. Similar to how TypeScript examines runtime values through static types, it also performs type analysis on JavaScript's runtime control flow constructs, such as if/else statements, conditional ternaries, loops, and truthiness checks, all of which can impact the types.

Within the if statement, TypeScript identifies the expression typeof value === "number" as a specific form of code known as a type guard. TypeScript analyzes the most specific type of a value at a given position by tracing the potential execution paths that the program can take. It is analogous to having a single starting point and then branches of possible outcomes. TypeScript examines these unique checks, called type guards, and assignments to create outcomes. This process of refining types (string or number) to be more precise than initially declared (string | number) is referred to as narrowing.

In conclusion, using type guards in your TypeScript code is essential for writing safer, more efficient, and more readable code. By narrowing the type of a variable within a specific context, you can access the properties and methods of that type without the need for type assertions or manual type checking.

## 3.7. Overcomplicating Types

Overcomplicated types can be a common pitfall in TypeScript projects. While complex types can sometimes be necessary, overcomplicating them can lead to confusion, decreased readability, and increased maintenance costs. In this chapter, we will discuss the problems associated with overcomplicated types and provide suggestions on how to simplify them.

### 3.7.1. Nested types

When you have deeply nested types, it can be challenging to understand their structure, which can lead to mistakes and increased cognitive load when working with them. The following example has a type defined by an interface (can be type alias too) with _three_ levels of nested properties:

```typescript
interface NestedType {
  firstLevel: {
    secondLevel: {
      thirdLevel: {
        value: string;
      };
    };
  };
}
```

To simplify this deeply nested type, consider breaking them down into smaller, more manageable types (using interfaces or type aliases if you prefer):

```typescript
interface ThirdLevel {
  value: string;
}

interface SecondLevel {
  thirdLevel: ThirdLevel;
}

interface FirstLevel {
  secondLevel: SecondLevel;
}

interface SimplifiedNestedType {
  firstLevel: FirstLevel;
}
```

### 3.7.2. Complex union and intersection types

Union and intersection types are powerful features in TypeScript but overusing them can lead to convoluted and difficult-to-understand types. Here's an example of some crazy complex type that has a lot of unions:

type ComplexType = string | number | boolean | null | undefined | Array<string>;

To simplify complex union and intersection types, consider using named types or interfaces to improve readability. We can refactor our complex type to a more readable one. The added benefit that the new types can be reused elsewhere in the project:

```typescript
type PrimitiveType = string | number | boolean;
type NullableType = null | undefined;
type SimplifiedComplexType = PrimitiveType | NullableType | Array<string>;
```

Here are the examples of valid assignments using the aforementioned types:

```typescript
let a: SimplifiedComplexType;
a = "hello";        // string
a = 42;             // number
a = true;           // boolean
a = null;           // null
a = undefined;      // undefined
a = ["apple", "banana", "cherry"]; // Array<string>
```

Or suppose we have an application that deals with user settings for notifications, profile information, and application preferences. Here are the original complex and potentially conflicting types:

```typescript
type NotificationSettings = {
  email: boolean;
  push: boolean;
  frequency: "daily" | "weekly" | "monthly";
};

type ProfileSettings = {
  displayName: string;
  biography: string;
  email: string;                          // #A
};

type AppPreferences = {
  theme: "light" | "dark";
  language: string;
  advancedMode: boolean;
};

type UserSettings = NotificationSettings 
  & ProfileSettings 
  & AppPreferences;                       // #B
```

`#A Potential conflict with NotificationSettings`
`#B Unnecessarily complex intersection type`

In this example, the UserSettings type becomes overly complicated and even has a potential conflict with the email property being present in both NotificationSettings and ProfileSettings. Here's how UserSettings type might be refactored for clariy:

```typescript
type NotificationSettings = {   
  notifications: {
    email: boolean;           // #A
    push: boolean;
    frequency: "daily" | "weekly" | "monthly";
  };
};

type ProfileSettings = {
  profile: {
    displayName: string;
    biography: string;
    contactEmail: string;     // #B
  };
};

type AppPreferences = {
  preferences: {
    theme: "light" | "dark";
    language: string;
    advancedMode: boolean;
  };
};

type UserSettings = NotificationSettings // #C
  & ProfileSettings 
  & AppPreferences;
```

`#A Email just for notifications`
`#B Renamed to avoid confusion with notification email settings`
`#C Composed type for user setting`


With this approach we avoid collisions and have nicely nested properties. 

### 3.7.3. Overuse of mapped and conditional types

Mapped and conditional types offer great flexibility, but overusing them can create overly complicated types that are difficult to read and maintain.

```typescript
type Overcomplicated<T extends { [key: string]: any }> = {
  [K in keyof T]: T[K] extends object ? Overcomplicated<T[K]> : T[K];
};
```

The TypeScript code provided defines a generic type Overcomplicated. This type recursively maps over the properties of an object type T and applies itself to any properties that are object types. This is the mechanism behind it:

- Generic Type T: The type Overcomplicated is a generic type. It expects a type parameter T which is constrained to be an object type (i.e., { [key: string]: any }). This means T must be an object with string keys and values of any type.

- Mapped Type: Inside the Overcomplicated type, there's a mapped type ([K in keyof T]). This part of the code iterates over all the keys (K) in the type T.

- Conditional Type: For each key K, the type of the corresponding property is checked:

  - If T[K] (the type of the property at key K in T) is an object (T[K] extends object), then Overcomplicated is recursively applied to this property. It means if a property is an object, the Overcomplicated type is again used for that object, allowing nested objects to be recursively processed in the same manner.

  - If T[K] is not an object (meaning it's a primitive type like string, number, etc.), then it retains its original type (T[K]).

In essence, this type leaves primitive types as they are, but if a property is an object, it recursively applies the same process to that object, effectively creating a deeply nested type structure that mirrors the original type but with the same Overcomplicated logic applied at all object levels.

This type could be useful in scenarios where you need to apply some type transformation or check recursively through all properties of a nested object structure, especially in complex TypeScript applications.

To simplify the Overcomplicated type, we can modify it, so that it doesn't recursively apply itself to nested object properties. Instead, we can just retain the type of each property as is, whether it's a primitive type or an object. While not exact equivalents, this will make the type mapping more straightforward and less complex. Thus, a more straightforward version follows:

```typescript
type Simplified<T extends { [key: string]: any }> = {
  [K in keyof T]: T[K];
};
```

Let's examine how this simplified version operates:

- Generic Type T: It still takes a generic type T which is an object with string keys and values of any type.

- Mapped Type: It maps over each property of T using [K in keyof T].

- Property Types: Instead of applying a conditional type to determine if the property is an object, it directly assigns the type T[K] to each property. This means each property in the resulting type will have the same type as it does in the original type T, whether that's a primitive type, an object, or any other type.

This approach streamlines the type definition by removing the recursive aspect and treating all properties uniformly, regardless of whether they're objects or primitives. It effectively creates a type that mirrors the structure of the original type without any additional complexity.

In conclusion, it's essential to strike a balance between the complexity and simplicity of your types. Overcomplicated types can decrease readability and increase maintenance costs, so be mindful of the structure and complexity of your types. Break down complex types into smaller, more manageable parts, and use named types or interfaces to improve readability.

## 3.8. Overlooking readonly Modifier

TypeScript provides the readonly modifier, which can be used to mark properties as read-only, meaning they can only be assigned a value during initialization and cannot be modified afterward. Neglecting to use readonly properties when appropriate can lead to unintended side effects and make code harder to reason about. This section will discuss the benefits of using readonly properties, provide examples of their usage, and share best practices for incorporating them into your TypeScript code.

The readonly modifier can be applied to properties in interfaces, types, and classes. Marking a property as readonly signals to other developers that its value should not be modified after initialization. But not only readonly signals, it also is enforced by TypeScript!

In this example, we have an interface readonly properties to illustrate the syntax (which is similar to other modifier like public):

```typescript
interface ReadonlyPoint {
  readonly x: number;
  readonly y: number;
}

const point: ReadonlyPoint = {
  x: 10,
  y: 10,
};

point.x = 12; //  #A
```

`#A Cannot assign to 'x' because it is a read-only property.`

We are not limited to just interfaces, we can use the modifier in the class definition and even combine with other modifiers like private and public.

```typescript
class ImmutablePerson {
  public readonly name: string;
  private readonly age: number;

  constructor(name: string, age: number) {
    this.name = name;        //  #A
    this.age = age;
  }
}

const person = new ImmutablePerson('Ivan', 18)
console.log(person.name)    //  #B
person.name = 'Dima'        //  #C
console.log(person.age)     //  #D

person.age = 17             //  #E
```

`#A Assignment in the constructor is okay for read-only properties`
`#B Reading public read-only is okay`
`#C Error: Cannot assign to 'name' because it is a read-only property.`
`#D Error: Property 'age' is private and only accessible within class 'ImmutablePerson'`
`#E Both errors: private and read-only`

And yes, readonly can be used with type aliases too! Here is a TypeScript code example illustrating the usage of a readonly modifier by having a readonly type alias with readonly properties and a class that`uses this type for its property center:

```typescript
type ReadonlyPoint = {    //  #A
  readonly x: number;
  readonly y: number;
};

const point: ReadonlyPoint = { //  #B
  x: 10, 
  y: 20 
}; 

point.x = 15;   //  #C

class Shape {   //  #D
  constructor(public readonly center: ReadonlyPoint) {
    //...
  }

  distanceTo(point: ReadonlyPoint): number {
    const dx = point.x - this.center.x;
    const dy = point.y - this.center.y;
    return Math.sqrt(dx * dx + dy * dy);
  }
}

const shape = new Shape(point);         //  #E
shape.center = { x: 0, y: 0 };          //  #F
shape.center.x = 0;

const anotherPoint: ReadonlyPoint = {   // #G
  x: 30, 
  y: 40 
}; 
const distance = shape.distanceTo(anotherPoint); // #H
```

`#A Type alias with read-only properties`
`#B Creating a ReadonlyPoint object`
`#C Error: Cannot assign to 'x' because it is a read-only property`
`#D Class that uses the ReadonlyPoint type for its property`
`#E Creating a Shape instance with the ReadonlyPoint object`
`#F Error: Cannot assign to 'center' because it is a read-only property.`
`#G Error: Cannot assign to 'x' because it is a read-only property.`
`#H Calculating the distance to another point`

In this example, we define a ReadonlyPoint type with readonly properties x and y. The Shape class uses the ReadonlyPoint type for its center property, which is also marked as readonly. The distanceTo method calculates the distance between the shape's center and another point. When we attempt to modify the x property of the point object, TypeScript raises an error because it is a readonly property.

If you would like to see a more realistic example of how readonly can help to prevent a bug, here's an example in which we have a class, then // Initialize the configuration with specific settings. Later in the code, trying to modify the configuration will lead to a compile-time error:

```typescript
class AppConfig {
  readonly databaseUrl: string;
  readonly maxConnections: number;

  constructor(databaseUrl: string, maxConnections: number) {
    this.databaseUrl = databaseUrl;
    this.maxConnections = maxConnections;
  }
}

const config = new AppConfig("https://db.example.com", 10);
config.databaseUrl = "https://db.changedurl.com";             //  #A
```

`#A This line will cause an error`

Note the distinguishing between readonly in lowercase and Readonly in uppercase. In TypeScript, these represent different concepts. While readonly refers to the modifier keyword discussed in this section, Readonly is a built-in utility type used for marking all properties in an object type as readonly.

The benefits of using read-only (readonly) properties are numerous:

- Immutability: readonly properties promote the use of immutable data structures, which can make code easier to reason about and reduce the likelihood of bugs caused by unintended side effects.

- Code clarity: Marking a property as readonly clearly communicates to other developers that the property should not be modified, making the code's intentions more explicit.

- Encapsulation: readonly properties help enforce proper encapsulation by preventing external modifications to an object's internal state.

To properly utilize readonly, keep in mind the best practices:

- Use readonly class properties for immutable data: Whenever you have data that should not change after initialization, consider using readonly properties. This is especially useful for objects that represent configuration data, constants, or value objects.

- Apply readonly to interfaces and types: When defining an interface or type, consider marking properties as readonly if they should not be modified. This makes the contract more explicit and helps ensure that the implementing code adheres to the desired behavior.

- Be cautious when using readonly arrays and any object and object-like type: When marking an array property as readonly, be aware that it only prevents the array reference from being changed, not the array's content. To create a truly immutable array, consider using ReadonlyArray<T> or the readonly modifier on array types.

Here's an illustration of making read only arrays:

```typescript
interface Data {
  readonly numbers: ReadonlyArray<number>;
}

// Alternatively

interface Data {
  readonly numbers: readonly number[];
}

const data: Data = {
  numbers: [1, 2, 3],
};

data.numbers = [];      //  #A
data.numbers[0] = 0;    //  #B
```

`#A readonly numbers gives an error: Cannot assign to 'numbers' because it is a read-only property.`
`#B ReadonlyArray and readonly T[] give an error: Index signature in type 'readonly number[]' only permits reading.`

However, we should emphasize that readonly T[]and ReadonlyArray<T> are _not_ "deep". For example, this code would allow to change the value val inside of an object which is an element of an arry:

```typescript
interface Item {
  val: number;
}

interface Data {
  readonly item: readonly Item[];
}

const objects: Data = {
  item: [{ val: 0 }, { val: 1 }],
};

objects.item[0].val = 1;        // #A
```

`#A Ok, no errors`

To fix this, we need to add readonly to val too:

```typescript
interface Item {
  readonly val: number;
}
```

Then we'll get an error: Cannot assign to 'val' because it is a read-only property.

By the way, here we can equally use type alias too, as in:

```typescript
type Data = {
  readonly numbers: ReadonlyArray<number>;
};
```

```typescript
type Data = {
  readonly numbers: readonly number[];
};
```

You may remember the shallow freeze standard JavaScript method Object.freeze() which also can be used to enforce immutability. However, freeze and readonly work in different ways and at different stages of the development process. Understanding the difference between freeze and readonly differences is key for choosing the right approach based on the context of your application. Let's discuss how they behave differently on these levels: Runtime, depth, context, error handling and intention.

Runtime vs. Compile-Time:

- Object.freeze(): This is a JavaScript method that works at runtime. It makes an object immutable after the object has been created. If you try to modify a frozen object at runtime, the operation will silently fail, or in strict mode, throw an error.

- readonly: This is a TypeScript feature that enforces immutability at compile time. It prevents properties of a class or interface from being reassigned after their initial assignment. TypeScript's readonly does not exist in the resulting JavaScript after compilation; it's purely a compile-time constraint.

Depth of Immutability:

- Object.freeze(): It is shallow, meaning it only applies to the immediate properties of the object. Nested objects are not frozen and can be modified.

- readonly: This keyword applies only to the property it is attached to. TypeScript does not have a built-in deep readonly feature. However, the immutability it enforces is part of the type system and is respected throughout the TypeScript code.

Usage Context:

- Object.freeze(): Used in both JavaScript and TypeScript. It is useful when you need to make an object immutable during runtime, perhaps after some initialization logic.

- readonly: Used exclusively in TypeScript. Ideal for class properties or interface fields that should not change after their initial setup, especially useful in large-scale applications where type safety is a priority.

Error Handling:

- Object.freeze(): Errors (in strict mode) or silent failures (in non-strict mode) occur at runtime if there is an attempt to modify the frozen object.

- readonly: Errors are thrown during the TypeScript compilation process, not at runtime.

Intention and Clarity:

- Object.freeze(): Typically used when an object needs to become immutable at a certain point in the program's execution.

- readonly: Clearly communicates to other developers that a property should not be modified after its initial value is set, enhancing code readability and maintainability.

Thus, Object.freeze() provides runtime immutability and is part of JavaScript, while readonly in TypeScript is a compile-time feature for preventing reassignment of properties. The choice between them depends on whether you need runtime enforcement or compile-time type safety.

To sum up this section and this mistake, these are two most common pitfalls to avoid when it comes to readonly in TypeScript:

- Forgetting to use readonly properties: Failing to use readonly properties when appropriate can lead to unintended side effects and make the code harder to reason about. Be mindful of the need for immutability and consider using readonly properties when immutability is necessary and/or preferred.

- Modifying readonly properties through aliases: Be cautious when passing readonly properties to functions or assigning them to variables, as they can still be modified through aliases. To prevent this, consider using Object.freeze() or deep freeze libraries for deep immutability.

By using readonly properties in your TypeScript code, you can promote immutability, improve code clarity, and enforce encapsulation. Be mindful of when to use readonly properties and consider applying them to interfaces, types, and classes as appropriate. This will result in more maintainable and robust code, reducing the likelihood of unintended side effects.

## 3.9. Forgoing keyof Utility Type

TypeScript provides a variety of powerful utility types that can enhance your code's readability and maintainability, and one of them is a utility type keyof. Neglecting to use this utility type when appropriate can lead to increased code complexity and missed opportunities for type safety. In this section, we will discuss the benefits of using keyof provide examples of its effective usage. Several other utility types will be covered in the following chapters, e.g., Pick and Partial in 6.6.

The keyof utility type is used to create a union of the property keys of a given type or interface. It can be particularly useful when working with object keys, enforcing type safety, and preventing typos or incorrect property access. Here's how it functions in detail:

```typescript
interface Person {
  name: string;
  age: number;
  hasPet: boolean;
}

type PersonKeys = keyof Person;     //  #A
```

`#A PersonKeys is "name" | "age" | "hasPet"`

Note: that we cannot make PersonKeys an interface in this example.

Next, observe the following illustration which increases type safety. We define an interface Person and then use it with keyof in a function signature to enforce that only the properties (keys) of this interface Person would be used in getProperty. If not, then we'll get an error "Argument of type ... is not assignable":

```typescript
interface Person {
  name: string;
  age: number;
  hasPet: boolean;
}

function getProperty(person: Person, key: keyof Person) {
  return person[key];
}

const person: Person = {
  name: "Sergiu",
  age: 40,
  hasPet: true
};

console.log(getProperty(person, "name"));        //  #A
console.log(getProperty(person, "address"));      //  #B
```

`#A Sergiu --- Correct usage`
`#B undefined --- Error: Argument of type '"address"' is not assignable to parameter of type 'keyof Person'`

In the example above, using keyof Person for the key parameter enforces type safety and ensures that only valid property keys can be passed to the getProperty function. In other words, TypeScript will warn us that fields (such as address) that are not in Person type are not allowd.

It's worth mentioning that we can modify the getProperty function to be general, that is suited for any type not just Person:

```typescript
function getProperty<T, K extends keyof T>(person: T, key: K) {
  return person[key];
}
```

We'll cover more on Generics and of course mistakes with Generics in chapter 6.

## 3.10. Underutilizing Utility Types Extract and Partial When Working with Object Types

There are two utilities that can be very helpful when working with types in TypeScript: Extract and Partial. Let's dive into them.


### 3.10.1. Ignoring Extract for narrowing types

In TypeScript, the Extract type is a conditional type that allows you to construct a type by extracting from a union all members that are assignable to another type. Essentially, it "extracts" from Type all union members that are assignable to Union:

```typescript
Extract<Type, Union>;
```

This can be useful when filtering types or working with overlapping types, or when working with libraries where you prefer to narrow down the types from a broader set. As a short example, suppose you have a union type of all AvailableColors, and you want to create a type PrimaryColors representing only certain members of that union:

```typescript
type AvailableColors = "red" | "green" | "blue" | "yellow";
type PrimaryColors = Extract<AvailableColors, "red" | "blue">;

let primary: PrimaryColors;
primary = "red"; // This is fine
primary = "blue"; // This is also fine
primary = "green"; // Error: Type '"green"' is not assignable to type 'PrimaryColors'
```

Following this, let's say we have an interface User that contains all the user information including some very private information that shouldn't be shared freely. Next, we can use Extract to create type SimpleUser and use this type to enforce that only select properties (keys) are being used to avoid leaking private user information.

```typescript
interface User {
  id: number;
  name: string;
  role: string;
  address: string;
  age: number;
  email: string;
  createdAt: string;
  updatedAt: string;
  dob: Date;
  phone: string;
}

type SimpleUser = Extract<keyof User, "id" | "name" | "role">; //  #A
const simpleUserProperties = ["id", "name", "role"];

function simplify(user: User): SimpleUser {                   //  #B
  
  return Object.keys(user)
    .reduce((obj: any, curr: string) => {
      if (simpleUserProperties.includes(curr))
        obj[curr] = user[curr as keyof User];
      return obj;
  }, {}) as SimpleUser;
}

const fakeUser: User = {
  id: 12345,
  name: "John Doe",
  role: "Admin",
  address: "1234 Elm Street, Anytown, USA",
  age: 35,
  email: "johndoe@example.com",
  createdAt: "2023-01-01T00:00:00.000Z",
  updatedAt: "2023-01-15T00:00:00.000Z",
  dob: new Date("1988-04-23"),
  phone: "555-1234",
};

const simpleUser: SimpleUser = simplify(fakeUser);
console.log(simpleUser);                                    //  #C
```

`#A The new type that has only select keys with Extract`
`#B Function that takes the full User object and returns a simplified object`
`#C { "id": 12345, "name": "John Doe", "role": "Admin" }`

What's interesting is that we can also pass results of keyof to Extract. Let's see it in the next example. Imagine that we need to create a function that would check properties between two user objects. We would define two interfaces and then use Extract to create type SharedProperties to enforce that only the properties (keys) of both interfaces will be used. Otherwise, I would get an error like we have in the example when we try to use email that is not present in one of the interfaces (but id is present in both, so it's fine).

```typescript
interface User {
  id: number;
  name: string;
  role: string;
}

interface Admin {
  id: number;
  name: string;
  role: string;
  permissions: string;
}

type SharedProperties = Extract<keyof User, keyof Admin>;

function compareUsers(
  user: User,
  admin: Admin,
  key: SharedProperties
): boolean {
  return user[key] === admin[key];
}

const user: User = {
  id: 10,
  name: "Zhang",
  role: "user",
};

const admin: Admin = {
  id: 1,
  name: "Zhang",
  role: "admin",
  permissions: "read, write",
};

console.log(compareUsers(user, admin, "id"));          //  #A
console.log(compareUsers(user, admin, "name"));        //  #B
console.log(compareUsers(user, admin, "permissions")); //  #C
```

`#A false --- Correct usage, no errors`
`#B false --- Error: Argument of type '"permissions"' is not assignable to parameter of type 'SharedProperties'`
`#C true--- Correct usage, no errors`

In the example above, using Extract allows us to create a SharedProperties type that includes only the properties common to both User and Admin. This ensures that the compareUsers function can only accept shared property keys as its third parameter.

It should be noted that, in the preceding example we can substitute our Extract with this an intersection of type (&):

```typescript
type SharedProperties = keyof User & keyof Admin;
```

This is because they both function similarly when supplied with unions of strings (keys), that is they work as a Venn diagram overlapping area between two circles of unions' members. However, there's a difference between intersection and extract in other cases especially when working with object types. For example, intersection of two object types (UserBasicInfo and UserPermissions) will produce a type (CombinedUserProfile) that has all the properties of each object type:

```typescript
type UserBasicInfo = {
  id: number;                  //  #A
  name: string;                //  #A
  email: string;               //  #A
};

type UserPermissions = {
  canEdit: boolean;            //  #B
  canDelete: boolean;          //  #B
  accessLevel: number;         //  #B
};

type CombinedUserProfile = UserBasicInfo & UserPermissions; //  #C
const userProfile: CombinedUserProfile = {                  //  #D
  id: 123,
  //...
  canEdit: true,
};
```

`#A UserBasicInfo represents basic information about a user`
`#B UserPermissions represents permissions assigned to a user`
`#C CombinedUserProfile represents a user's profile along with their permissions`
`#D userProfile has all properties: id, name, email, canEdit, canDelete, accessLevel`

On the contrary, Extract with object types would be never, because UserBasicInfo is not assignable to UserPermissions:

```typescript
type ExtractionType = Extract<UserBasicInfo, UserPermissions>;
```

### 3.10.2. Avoiding Partial for marking properties as optional

In TypeScript, Partial is a built-in utility type that allows you to create a type that makes all properties of another type optional. This is useful when you want to create an object that doesn't necessarily have values for all properties initially but may have them added later on. Or, when you're sending an update to a data store (e.g., database) only for some properties, not all of them.

Here's an example of how you can use Partial to auto-magically create a new type that will have properties of the original types but with all these properties becoming optional:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<User>;       //  #A
let user: PartialUser = {};             //  #B

user.id = 1;                            //  #C
user.name = "Aisha";

console.log(user);                      //  #D
```

`#A You can define a 'PartialUser' type that has all the same properties as 'User', but all are optional`
`#B Empty object assignment is valid because all properties are optional`
`#C You can add properties one by one and still it'll work`
`#D Output: { id: 1, name: 'Aisha }`

In this example, PartialUser is a type that has the same properties as User, but all of them are optional. This means you can create a PartialUser object without any properties, and then add them one by one.

This can be very useful when working with functions that update objects, where you only want to specify the properties that should be updated. For example:

```typescript
function updateUser(user: User, updates: Partial<User>): User {
  return { ...user, ...updates };
}

let user: User = { id: 1, name: "Alice", email: "alice@example.com" };
let updatedUser = updateUser(user, { email: "newalice@example.com" });

console.log(updatedUser);               //  #A
```

`#A { id: 1, name: 'Alice', email: 'newalice@example.com' }`

In this example, updateUser is a function that takes a User and a Partial<User> and returns a new User with the updates applied. This allows you to update a user's email without having to specify the id and name properties.

In conclusion, leveraging utility types like Extract and Partial can help you write cleaner, safer, and more maintainable TypeScript code. Be sure to take advantage of this utility type when appropriate to enhance your code's readability and type safety.

## 3.11. Summary

- Use interfaces to define object shape. Interfaces can be extended and reopened while type aliases cannot be.

- Use type aliases for complex types, intersections, unions, tuples, etc.

- Simplify interfaces by removing empty ones, and merging others when it makes sense. Consider using intersection types or defining entirely new interfaces where appropriate.

- Maintain consistent property ordering in object literals and interfaces. Name properties consistently across the classes, types and interfaces for improved readability.

- Use type safe guards instead of type assertions (as). Implement type guards where possible to provide clearer, safer code.

- When needed, use explicit annotations to prevent type widening and ensure your variables always have the expected type, because TypeScript automatically widens types in certain situations, which can lead to unwanted behavior.

- Leverage readonly when it makes sense to prevent property mutation that is to ensure that once a property is initialized, it can't be changed. It helps in preventing accidental mutation of properties and enforces immutability.

- Utilize keyof and extract to enforce checks on property (key) names. keyof can be used to get a union of a type's keys, and Extract can extract specific types from a union.


