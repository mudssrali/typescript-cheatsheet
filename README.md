<p align="center"><img src="./typescript.png" width="64" height="64" alt="header-image"></p>
<h2 align="center">Easy cheatsheet to learn Modern Typescript</h2>

<p align="center"
  <a href="https://github.com/mudassar045/typescript-cheatsheet">
  <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs Welcome">
  </a>
</p>

# Typing Objects

## `Object` vs `object`

-   **Object** is the type of all instances of class **Object** _OR_ Contains stuff (like `toString()`, `hasOwnProperty()`) that is present in all JavaScript objects. Any value (primitive, non-primitive) can be assigned to `Object` type.

    -   It describes functionality that is common to all JavaScript objects
    -   It includes primitive values

    ```ts
    const obj = {};
    obj instanceof Object; // true
    obj.toString === Object.prototype.toString; // true

    declare function create(x: Object);

    create("string"); // OK

    create(null); // Error
    create(undefined); // Error
    ```

-   **object** is the type of all non-primitive values. You can't assign to it any primitive type like `boolean`, `number`, `string`, `bigint`, `symbol`, `null` or `undefined`

    ```ts
    declare function create(x: object);

    create([]); // OK
    create({ id: 0 }); // OK

    create(42); // Error
    create("string"); // Error
    create(false); // Error
    create(undefined); // Error
    ```

-   **{}** is an empty object. It is the same as `Object`

## Interface Signatures Overview

```ts
interface RepoInterface {
    isPublic: boolean; // property signature

    hasRequests(x: string): void; // method signature, 'x' for documentation only
    hasRequests: (x: string) => void; // method signature for ES6

    [propName: string]: any; // index signature
    (x: number): string; // call signature
    new (x: string): RepoInstance; // construct signature

    readonly admin: string; // readonly modifier
    readonly [index: number]: string; // you can make index readonly like this

    isArchive?: string; // optional modifier, auto set to undefined
    isArchive: undefined; // in this case need set the  property
}
```

### Index Signature

Helps to describe Arrays or objects that are used as dictionaries.

If there are both an index signature and property and/or method signatures in an interface, the type of the index property value must also be a `supertype` of the type of the property value and/or method

```ts
interface RepoInterface {
    [propName: string]: boolean;

    // 'number' is not assignable to string index type 'boolean'
    star: number;

    // '() => string' is not assignable to string index type 'boolean'
    isCreated(): string;
}

// You can resolve above problem like this

interface RepoInterface {
    [propName: string]: number;
    star: number; // OK
    isCreated(): number; // OK
}

// Or you can do this to your interface

interface RepoInterface {
    [propName: string]: boolean | number | string;
    star: number; // OK
    isCreated(): string; // OK
}
```

### Call Signature

Enables interfaces to describe functions. **NOTE** `this` is the optional calling context of the function in this example:

```ts
interface ClickListener {
    (this: Window, e: MouseEvent): void;
}

const myListener: ClickListener = (e) => {
    console.log("mouse clicked!", e);
};

addEventListener("click", myListener);
```

### Construct Signature

Enables describing classes and constructor functions. A class has two types:

-   The type of the static side
-   The type of the instance side

The constructor sits in the static side, when a class implements an interface,
only the instance side of the class is checked.

```ts
interface ClockInterface {
  tick(): void
}

interface ClockConstructor {
  new (h: number, m: number): ClockInterface
}

/*
* Using Class Expression
*/

const ClockA: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {...}
  tick() {...}
}

const clockClassExp = new ClockA(18, 11)

/*
* Using Class Declaration with a Constructor Function
*/

class ClockB implements ClockInterface {
  constructor(h: number, m: number) {...}
  tick() {...}
}

function createClock( ctor: ClockConstructor, h: number, m: number): ClockInterface {
  return new ctor(h, m)
}

const clockClassDeclaration = createClock(ClockB, 12, 17)
```

**[Typescript Docs - Class Type](https://www.typescriptlang.org/docs/handbook/interfaces.html#class-types)**

## Type Literal Syntax

Typically used in the signature of a higher-order function, but it's not limited to this.

```ts
type Point = {
    x: number;
    y: number;
};

type SetPoint = (x: number, y: number) => void;
```

## Excess Properties

-   Engineers **can’t** just think of interfaces as “objects that have exactly a
    set of properties” or “objects that have at least a set of properties”.
    In-line object arguments receive an additional level of validation that
    doesn’t apply when they’re passed as variables.

-   TypeScript is a **structurally** typed language. To create a `Dog` you don’t
    need to explicitly extend the `Dog` interface, any object with a `breed`
    property that is of type `string` can be used as a `Dog`:

<!-- end list -->

```ts
interface Dog {
    breed: string;
}

function printDog(dog: Dog) {
    console.log("Dog: " + dog.breed);
}

const ginger = {
    breed: "Airedale",
    age: 3,
};

printDog(ginger); // excess properties are OK!

printDog({ breed: "Airedale", age: 3 }); // ERRORS

/*
  Argument of type '{ breed: string; age: number }' is not assignable to parameter of type 'Dog'.
  Object literal may only specify known properties, and 'age' does not exist in type 'Dog'.
*/

// To get rid of above problem, you can define interface with extra proprty with string index signature

interface Dog {
    breed: string;
    [propName: string]: any;
}
```

## Interface vs Type

Unlike an **interface** declaration, which always introduces a named `object` type, a **type** alias declaration can introduce a name for any kind of type, including `primitive`, `union`, and `intersection` types. With examples, you can find some in-depth difference between `interface` and `type`.

-   `Objects / Functions`

    Both can be used to describe the shape of an object or a function signature. But the syntax differs.

    **Interface**

    ```ts
    interface Point {
        x: number;
        y: number;
    }

    interface SetPoint {
        (x: number, y: number): void;
    }
    ```

    **Type alias**

    ```ts
    type Point = {
        x: number;
        y: number;
    };

    type SetPoint = (x: number, y: number) => void;
    ```

-   `Other Types`

    Unlike an interface, the type alias can also be used for other types such as `primitives`, `unions`, and `tuples` (Aforementioned).

    ```ts
    // primitive
    type Name = string;

    // object
    type PartialPointX = { x: number };
    type PartialPointY = { y: number };

    // union
    type PartialPoint = PartialPointX | PartialPointY;

    // tuple
    type Data = [number, string];
    ```

-   Extend

    Both can be extended, but again, the syntax differs. Additionally, note that an interface and type alias are not mutually exclusive. An interface can extend a type alias, and vice versa.

    **interface extends interface**

    ```ts
    interface PartialPointX {
        x: number;
    }
    interface Point extends PartialPointX {
        y: number;
    }
    ```

    **type alias extends type alias**

    ```ts
    type PartialPointX = { x: number };
    type Point = PartialPointX & { y: number };
    ```

    **interface extends type alias**

    ```ts
    type PartialPointX = { x: number };
    interface Point extends PartialPointX {
        y: number;
    }
    ```

    **type alias extends interface**

    ```ts
    interface PartialPointX {
        x: number;
    }
    type Point = PartialPointX & { y: number };
    ```

-   `Implements`

    A class can implement an interface or type alias, both in the same exact way. Note however that a class and interface are considered static blueprints. Therefore, they can not `implement / extend` a type alias that names a **union type**.

    ```ts
    interface Point {
        x: number;
        y: number;
    }

    class SomePoint implements Point {
        x = 1;
        y = 2;
    }

    type Point2 = {
        x: number;
        y: number;
    };

    class SomePoint2 implements Point2 {
        x = 1;
        y = 2;
    }

    type PartialPoint = { x: number } | { y: number };

    // ERROR: can not implement a union type
    class SomePartialPoint implements PartialPoint {
        x = 1;
        y = 2;
    }
    /*
    A class can only implement an object type or intersection of object types with statically known members.
    */
    ```

-   `Declaration merging`

    Unlike a type alias, an interface can be defined multiple times, and will be treated as a single interface (with members of all declarations being merged).

    ```ts
    // These two declarations become:
    // interface Point { x: number y: number }
    interface Point {
        x: number;
    }
    interface Point {
        y: number;
    }

    const point: Point = { x: 1, y: 2 };
    ```

# Mapped Types - Getting Types from Data

## `typeof` / `keyof` Examples

```ts
const data = {
    value: 123,
    text: "text",
    subData: {
        value: false,
    },
};

type Data = typeof data; // Data = { value: number; text: string; subData: { value: boolean; } }
```

```ts
const data = ["A", "B"] as const;
type Data = typeof data[number]; // "A" | "B"
```

```ts
const locales = [
    {
        locale: "se",
        language: "Swedish",
    },
    {
        locale: "en",
        language: "English",
    },
] as const;

type Locale = typeof locales[number]["locale"]; // "se" | "en"
```

```ts
const currencySymbols = {
    GBP: "£",
    USD: "$",
    EUR: "€",
};
type CurrencySymbol = keyof typeof currencySymbols; // "GBP" | "USD" | "EUR"
```

## `keyof` with Generics and Interfaces Example

**Exampl-1**:

```ts
interface HasPhoneNumber {
    name: string;
    phone: number;
}

interface HasEmail {
    name: string;
    email: string;
}

interface CommunicationMethods {
    email: HasEmail;
    phone: HasPhoneNumber;
    fax: { fax: number };
}

function contact<K extends keyof CommunicationMethods>(
    method: K,
    contact: CommunicationMethods[K] // turning key into value - a mapped type
) {
    // do something...
}

contact("email", { name: "foo", email: "mike@example.com" });
contact("phone", { name: "foo", phone: 3213332222 });
contact("fax", { fax: 1231 });

// // we can get all values by mapping through all keys
type AllCommKeys = keyof CommunicationMethods;
type AllCommValues = CommunicationMethods[keyof CommunicationMethods];
```

**Exampl-2**:

Let's take a `prop` function

```ts
function prop<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

const todo = {
    id: 1,
    text: "Buy milk",
    due: new Date(2016, 11, 31),
};

const id = prop(todo, "id"); // number
const text = prop(todo, "text"); // string
const due = prop(todo, "due"); // Date
```

## Lookup Types

```ts
interface Person {
    name: string;
    age: number;
    location: string;
}

type P1 = Person["name"]; // string
type P2 = Person["name" | "age"]; // string | number

// typing from string operations

type P3 = string["charAt"]; // (pos: number) => string
type P4 = string[]["push"]; // (...items: string[]) => number
type P5 = string[][0]; // string
```

Article Links:

-   [Keyof and Lookup by Marius Schulz](https://mariusschulz.com/blog/keyof-and-lookup-types-in-typescript)
-   [More Read about Mapped Types](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#mapped-types)

-   [More Read about Keyof and Lookup Types](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#keyof-and-lookup-types)

# Immutability

## `readonly` Properties

Properties marked with `readonly` can only be assigned to during initialization
or from within a constructor of the same class.

```ts
type Point = {
    readonly x: number;
    readonly y: number;
};

const origin: Point = { x: 0, y: 0 }; // OK
origin.x = 100; // Error

function moveX(p: Point, offset: number): Point {
    p.x += offset; // Error
    return p;
}

function moveX(p: Point, offset: number): Point {
    // OK
    return {
        x: p.x + offset,
        y: p.y,
    };
}
```

## `readonly` Class Properties

Gettable area property is implicitly read-only because there’s no setter:

```ts
class Circle {
    readonly radius: number;

    constructor(radius: number) {
        this.radius = radius;
    }

    get area() {
        return Math.PI * this.radius ** 2;
    }
}
```

## `readonly` Array / Tuple

Here are different usecases of readonly

-   readonly property

    ```ts
    interface Apple {
      readonly types: string[]
      readonly origin: [string, string]
    }

    const apple: Apple = {
      types: ["Asian", "American", "European"]
      origin: ["Local", "Home Grown"]
    }

    apple.types.push("Russian") // OK, array is now mutable
    ```

-   property has readonly type

    ```ts
    interface Apple {
      types: readonly string[]
      origin: readonly [string, string]
    }

    const apple: Apple = {
      types: ["Asian", "American", "European"]
      origin: ["Local", "Home Grown"]
    }

    apple.types.push("Russian") // Error, Property `push` does not exist on type 'readonly string[]'
    ```

-   Variable declaration with readonly

    ```ts
    const array: readonly string[];
    const tuple: readonly [string, string];
    ```

## `const` Assertions

-   `number` becomes number literal

    ```ts
    // Type '10'
    let num = 10 as const;
    ```

-   array literals become `readonly` tuples

    ```ts
    // Type 'readonly [10, 20]'
    let tuple = [10, 20] as const;
    ```

-   object literals get `readonly` properties
-   no literal types in that expression should be widened (e.g. no going from `"hello"` to `string`)

    ```ts
    // Type '{ readonly text: "hello" }'
    let input = { text: "hello" } as const;
    ```

-   object literals with array types becomes also readonly

    ```ts
    // Type `{ readonly types: readonly ["A", "B"]}`
    let apple = { types: ["A", "B"] } as const;

    apple.types = ["C"]; // Error, 'types' is readonly, we can't reassign
    apple.push("C"); // Error, Property 'push' does not exist on type 'readonly ["A", "B", "C"]'
    ```

-   ⛔ `const` contexts **don’t** immediately convert an expression to be fully `immutable`.

    ```ts
    let types = ["Asian", "European"];

    let apple = {
        name: "Green Apple",
        types: types,
    } as const;

    apple.name = "Red Apple"; // Error
    apple.types = ["American"]; // Error

    aaple.types.push("African"); // OK

    // to fix above, just do this little trick

    let types = ["Asian", "European"] as const;
    // OR
    let types: readonly string[] = ["Asian", "European"];
    ```

# Strict Mode

```json
  strict: true /* Enable all strict type-checking options. */
```

is equivalent to enabling all of the strict mode family options:

```json
  noImplicitAny: true /* Raise error on expressions and declarations with an implied 'any' type */,
  strictNullChecks: true /* Enable strict null checks */,
  strictFunctionTypes: true /* Enable strict checking of function types */,
  strictBindCallApply: true /* Enable strict 'bind', 'call', and 'apply' methods on functions */,
  strictPropertyInitialization: true /* Enable strict checking of property initialization in classes */,
  noImplicitThis: true /* Raise error on 'this' expressions with an implied 'any' type */,
  alwaysStrict: true /* Parse in strict mode and emit "use strict" for each source file */
```

You can then turn off individual strict mode family checks as needed.

## Non-Nullable Types `--strictNullChecks`

In strict null checking mode, `null` and `undefined` are no longer assignable to
every type.

```ts
let name: string;
name = "Marius"; // OK
name = null; // Error
name = undefined; // Error
```

```ts
let name: string | null;
name = "Marius"; // OK
name = null; // OK
name = undefined; // Error
```

Optional parameter `?` automatically adds `| undefined`

```ts
type User = {
    firstName: string;
    lastName?: string; // same as `string | undefined`
};
```

-   In JavaScript, every function parameter is optional, when left off their value
    is `undefined`.
-   We can get this functionality in TypeScript by adding a `?` to the end of
    parameters we want to be optional. This is different from adding `| undefined`
    which requires the parameter to be explicitly passed as `undefined`

```ts
function fn1(x: number | undefined): void {
  ...
}

function fn2(x?: number): void {
  ...
}

fn1() // Error
fn2() // OK
fn1(undefined) // OK
fn2(undefined) // OK
```

Type guard needed to check if Object is possibly `null`:

```ts
function getLength(s: string | null) {
    // Error: Object is possibly 'null'.
    return s.length;
}
```

```ts
function getLength(s: string | null) {
    if (s === null) {
        return 0;
    }
    return s.length;
}

// JS's truthiness semantics support type guards in conditional expressions
function getLength(s: string | null) {
    return s ? s.length : 0;
}
```

```ts
function doSomething(callback?: () => void) {
    // Error: Object is possibly 'undefined'.
    callback();
}
```

```ts
function doSomething(callback?: () => void) {
    if (typeof callback === "function") {
        callback();
    }
}
```

## Strict Bind Call Apply `--strictBindCallApply`

> The `call()` method calls a function with a given `this` value and arguments
> provided individually, while `apply()` accepts a single array of arguments.
>
> The `bind()` method creates a new function that, when called, has its `this`
> keyword set to the provided value.

When set, TypeScript will check that the built-in methods of functions `call`,
`bind`, and `apply` are invoked with correct argument for the underlying
function:

```ts
// With strictBindCallApply on
function fn(x: string) {
    return parseInt(x);
}

const n1 = fn.call(undefined, "10"); // OK
const n2 = fn.call(undefined, false); // Argument of type 'false' is not assignable to parameter of type 'string'.
```

## Strict Class Property Initialization `--strictPropertyInitialization`

Verify that each instance property declared in a class either:

-   Has an explicit initializer, or
-   Is definitely assigned to in the constructor

```ts
// Error
class User {
    // Type error: Property 'username' has no initializer
    // and is not definitely assigned in the constructor
    username: string;
}

// OK
class User {
    username = "n/a";
}

const user = new User();
const username = user.username.toLowerCase();

// OK
class User {
    constructor(public username: string) {}
}

const user = new User("adam");
const username = user.username.toLowerCase();
```

-   Has a type that includes undefined

```ts
class User {
    username: string | undefined;
}

const user = new User();

// Whenever we want to use the username property as a string, we first have
// to make sure that it actually holds a string, not the value undefined
const username =
    typeof user.username === "string" ? user.username.toLowerCase() : "n/a";
```

# Types

## `never`

`never` represents the type of values that never occur. It is used in the
following two places:

-   As the return type of functions that never return
-   As the type of variables under type guards that are never true

`never` can be used in control flow analysis:

```ts
function controlFlowAnalysisWithNever(value: string | number) {
    if (typeof value === "string") {
        value; // Type string
    } else if (typeof value === "number") {
        value; // Type number
    } else {
        value; // Type never
    }
}
```

## `unknown`

`unknown` is the type-safe counterpart of the `any` type: we have to do some
form of checking before performing most operations on values of type `unknown`.

### Reading `JSON` from `localStorage` using `unknown` Example

```ts
type Result =
    | { success: true; value: unknown }
    | { success: false; error: Error };

function tryDeserializeLocalStorageItem(key: string): Result {
    const item = localStorage.getItem(key);

    if (item === null) {
        // The item does not exist, thus return an error result
        return {
            success: false,
            error: new Error(`Item with key "${key}" does not exist`),
        };
    }

    let value: unknown;

    try {
        value = JSON.parse(item);
    } catch (error) {
        // The item is not valid JSON, thus return an error result
        return {
            success: false,
            error,
        };
    }

    // Everything's fine, thus return a success result
    return {
        success: true,
        value,
    };
}
```

# Generics

Generics enable you to create reusable code components that work with a number
of types instead of a single type.

## With and Without Type Argument Inference

```ts
function identity<T>(arg: T): T {
    return arg;
}

let output = identity<string>("myString"); // type of output will be 'string'
let output = identity("myString"); // type argument inference
// compiler sets the value of `T` based on the type of the argument we pass in
```

## Working with Generic Type Variables

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length); // Error: T doesn't have .length
    return arg;
}

// to specify .length property we should make generic T to T[] or Array<T>

function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length); // Array has a .length, so no more error
    return arg;
}
```

## Using More Than One Type Argument

No value arguments are needed in this case:

```ts
function makePair<F, S>() {
    let pair: { first: F; second: S };

    function getPair() {
        return pair;
    }

    function setPair(x: F, y: S) {
        pair = {
            first: x,
            second: y,
        };
    }
    return { getPair, setPair };
}

// Creates a (number, string) pair
const { getPair, setPair } = makePair<number, string>();

setPair(1, "y"); // must be type of (number, string)
getPair(); // will return pair of type { number, string }
```

## Higher Order Function with `Parameters<T>` and `ReturnType<T>`

```ts
// Input a function `<T extends (...args: any[]) => any>`
// Output a function with same params and return type `:(...funcArgs: Parameters<T>) => ReturnType<T>`

function logDuration<T extends (...args: any[]) => any>(func: T) {
    const funcName = func.name;

    // Return a new function that tracks how long the original took
    return (...args: Parameters<T>): ReturnType<T> => {
        console.time(funcName);
        const results = func(...args);
        console.timeEnd(funcName);
        return results;
    };
}

function addNumbers(a: number, b: number): number {
    return a + b;
}
// Hover over is `addNumbersWithLogging: (a: number, b: number) => number`
const addNumbersWithLogging = logDuration(addNumbers);

addNumbersWithLogging(5, 3);
```

A generic class has a similar shape to a generic interface. Generic classes have a generic type parameter list in angle brackets (`<>`) following the name of the class.

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();

myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
    return x + y;
};
```

# Discriminated Unions

Discriminated Unions provide a powerful pattern in TypeScript. Immensely useful for actions & reducers in `ngrx/redux`, and every time you have to distinguish between `kinds` of objects. They enaWtype inference which, combined with strict null checks, will catch a lot of bugs! [By Minko Gechev](https://twitter.com/mgechev/status/1255021510563115008)

```ts
const enum Entity {
    Individual,
    Corporation,
}

interface Individual {
    type: Entity.Individual;
    ssn: string;
}

interface Corporation {
    type: Entity.Corporation;
    ein: string;
}

type TaxPayer = Individual | Corporation;

function magic(payer: TaxPayer) {
    if (payer.type === Entity.Individual) {
        taxIdentifier(payer.ssn);
        taxIdentifier(payer.ein); // Property 'ein' does not exist on type 'Individual'
    } else {
        taxIdentifier(payer.ein);
        taxIdentifier(payer.ssn); // Property 'ssn' doesn't exist on type 'Corporation'
    }
}
```

# Optional Chaining

## `?.` returns `undefined` when hitting a `null` or `undefined`

Album where the artist, and the artists biography might not be present in the
data.

```ts
type AlbumAPIResponse = {
    title: string;
    artist?: {
        name: string;
        bio?: string;
        previousAlbums?: string[];
    };
};

// Instead of:
const maybeArtistBio = album.artist && album.artist.bio;

// ?. acts differently than && on "falsy" values: empty string, 0, NaN, false
const artistBio = album?.artist?.bio;

// optional chaining also works with the [] operators when accessing elements
// see more example for optional element access below
const maybeArtistBioElement = album?.["artist"]?.["bio"];
const maybeFirstPreviousAlbum = album?.artist?.previousAlbums?.[0];
```

Optional chaining on an optional function:

```ts
interface OptionalFunction {
    bar?: () => number;
}

const foo: OptionalFunction = {};
const bat = foo.bar?.(); // number | undefined
```

-   `Optional Element Access`

the _optional element access_ which acts similarly to _optional property_ accesses, but allows us to access non-identifier properties (e.g. arbitrary strings, numbers, and symbols):

```ts
/**
 * Get the first element of the array if we have an array.
 * Otherwise return undefined.
 */
function tryGetFirstElement<T>(arr?: T[]) {
    return arr?.[0];

    // equivalent to
    //   return (arr === null || arr === undefined) ?
    //       undefined :
    //       arr[0]
}
```

-   `Optional Call`

_optional call_, which allows us to conditionally call expressions if they’re not `null` or `undefined`.

```ts
async function makeRequest(url: string, log?: (msg: string) => void) {
    log?.(`Request started at ${new Date().toISOString()}`);

    // roughly equivalent to
    //   if (log != null) {
    //       log(`Request started at ${new Date().toISOString()}`)
    //   }

    const result = (await fetch(url)).json();

    log?.(`Request finished at at ${new Date().toISOString()}`);

    return result;
}
```

-   `Short-circutting`

The _short-circuiting_ behavior that optional chains have is limited property accesses, calls, element accesses - it doesn’t expand any further out from these expressions.

```ts
let result = foo?.bar / someComputation();
// doesn’t stop the division or someComputation() call from occurring. It’s equivalent to

let temp = foo === null || foo === undefined ? undefined : foo.bar;

let result = temp / someComputation();
```

# Nullish Coalescing

## `??` “fall Backs” to a Default Value When Dealing with `null` or `undefined`

Value `foo` will be used when it’s “present”; but when it’s `null` or
`undefined`, calculate `bar()` in its place.

```ts
let x = foo ?? bar();
// instead of
let x = foo !== null && foo !== undefined ? foo : bar();
```

It can replace uses of `||` when trying to use a default value, and avoids bugs.
When `localStorage.volume` is set to `0`, the page will set the volume to `0.5`
which is unintended. `??` avoids some unintended behaviour from `0`, `NaN` and
`""` being treated as falsy values.

```ts
function initializeAudio() {
    let volume = localStorage.volume || 0.5; // Potential bug
}
```

# Comments

## ts-expect-error - 3.9

TypeScript 3.9 brings a new feature: `// @ts-expect-error` comments. When a line is prefixed with a `// @ts-expect-error` comment, TypeScript will suppress that error from being reported; but if there’s no error, TypeScript will report that `// @ts-expect-error` wasn’t necessary.

```ts
// @ts-expect-error
console.log(47 * "octopus"); // OK, no problem here

// @ts-expect-error
console.log(1 + 1); // Unused '@ts-expect-error' directive.
```

## ts-expect-error vs ts-ignore

In some ways `// @ts-expect-error` can act as a suppression comment, similar to `// @ts-ignore`. The difference is that `// @ts-ignore` will do nothing if the following line is error-free.

You might be tempted to switch existing `// @ts-ignore` comments over to `// @ts-expect-error`, and you might be wondering which is appropriate for future code. While it’s entirely up to you and your team, we have some ideas of which to pick in certain situations.

**Pick `ts-expect-error` if**:

-   you’re writing test code where you actually want the type system to error on an operation
-   you expect a fix to be coming in fairly quickly and you just need a quick workaround
-   you’re in a reasonably-sized project with a proactive team that wants to remove suppression comments as soon affected code is valid again

**Pick `ts-ignore` if**:

-   you have an a larger project and and new errors have appeared in code with no clear owner
-   you are in the middle of an upgrade between two different versions of TypeScript, and a line of code errors in one version but not another.
-   you honestly don’t have the time to decide which of these options is better

</article>

[:arrow_up: Back to top](#easy-cheatsheet-to-learn-modern-typescript)

Thanks goes to these people for great examples:

-   [Typescript Lang Organization](https://www.typescriptlang.org/)
-   [David-Else](http://github.com/David-Else)
-   [Marius Schulz](https://github.com/mariusschulz)
-   [Shu Uesugi](https://github.com/chibicode)
-   [Axel Rauschmayer](https://github.com/rauschma)
-   [Minko Gechev](https://github.com/mgechev)

**[MIT-LICENSE](./LICENSE)**
