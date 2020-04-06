<p align="center"><img src="./typescript.png" width="64" height="64" alt="header-image"></p>
<h2 align="center">Easy cheatsheet to learn Modern Typescript</h2>

<p align="center"
  <a href="https://github.com/mudassar045/typescript-cheatsheet">
  <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs Welcome">
  </a>
</p>

# Quick Links

<article class="markdown-body">

- [Typing Objects](#typing-objects)
  - [`Object` vs `object`](#object-vs-object)
  - [Interface Signatures Overview](#interface-signatures-overview)
    - [Index Signature](#index-signature)
    - [Call Signature](#call-signature)
    - [Construct Signature](#construct-signature)
  - [Type Literal Syntax](#type-literal-syntax)
  - [Excess Properties](#excess-properties)
  - [`interface` vs `type`](#interface-vs-type)
- [Mapped Types - Getting Types from Data](#mapped-types---getting-types-from-data)
  - [`typeof` / `keyof` Examples](#typeof--keyof-examples)
  - [`keyof` with Generics and Interfaces Example](#keyof-with-generics-and-interfaces-example)

# Typing Objects

## `Object` vs `object`

- **Object** is the type of all instances of class **Object** *OR* Contains stuff (like `toString()`, `hasOwnProperty()`) that is present in all JavaScript objects. Any value (primitive, non-primitive) can be assigned to `Object` type.

  - It describes functionality that is common to all JavaScript objects
  - It includes primitive values

  ```ts
  const obj = {}
  obj instanceof Object // true
  obj.toString === Object.prototype.toString // true

  declare function create(x: Object)

  create("string") // OK

  create(null) // Error
  create(undefined) // Error
  ```

- **object** is the type of all non-primitive values.  You can't assign to it any primitive type like `boolean`, `number`, `string`, `bigint`, `symbol`, `null` or `undefined`

  ```ts
  declare function create(x: object)

  create([]) // OK
  create({id: 0}) // OK

  create(42) // Error
  create("string") // Error
  create(false) // Error
  create(undefined) // Error
  ```

- **{}** is an empty object. It is the same as `Object`

## Interface Signatures Overview

  ```ts
  interface RepoInterface {
    isPublic: boolean // property signature

    hasRequests(x: string): void // method signature, 'x' for documentation only
    hasRequests: (x: string) => void // method signature for ES6

    [propName: string]: any // index signature
    (x: number): string // call signature
    new (x: string): RepoInstance // construct signature

    readonly admin: string // readonly modifier
    readonly [index: number]: string // you can make index readonly like this

    isArchive?: string // optional modifier, auto set to undefined
    isArchive: undefined // in this case need set the  property
  }
  ```

### Index Signature

Helps to describe Arrays or objects that are used as dictionaries.

If there are both an index signature and property and/or method signatures in an interface, the type of the index property value must also be a `supertype` of the type of the property value and/or method

```ts
interface RepoInterface {

  [propName: string]: boolean

  // 'number' is not assignable to string index type 'boolean'
  star: number

  // '() => string' is not assignable to string index type 'boolean'
  isCreated(): string
}

// You can resolve above problem like this

interface RepoInterface {
  [propName: string]: number
  star: number // OK
  isCreated(): number // OK
}

// Or you can do this to your interface

interface RepoInterface {
  [propName: string]: boolean | number | string
  star: number // OK
  isCreated(): string // OK
}

```

### Call Signature

Enables interfaces to describe functions. **NOTE** `this` is the optional calling context of the function in this example:

```ts
interface ClickListener {
  (this: Window, e: MouseEvent): void
}

const myListener: ClickListener = e => {
  console.log("mouse clicked!", e)
}

addEventListener("click", myListener)
```

### Construct Signature

Enables describing classes and constructor functions. A class has two types:

- The type of the static side
- The type of the instance side

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

Typically used in the signature of a higher-order function but it's not limited too.

```ts
type Point = {
  x: number
  y: number
}

type SetPoint = (x: number, y: number) => void
```

## Excess Properties

- Engineers **can’t** just think of interfaces as “objects that have exactly a
  set of properties” or “objects that have at least a set of properties”.
  In-line object arguments receive an additional level of validation that
  doesn’t apply when they’re passed as variables.

- TypeScript is a **structurally** typed language. To create a `Dog` you don’t
  need to explicitly extend the `Dog` interface, any object with a `breed`
  property that is of type `string` can be used as a `Dog`:

<!-- end list -->

```ts
interface Dog {
  breed: string
}

function printDog(dog: Dog) {
  console.log("Dog: " + dog.breed)
}

const ginger = {
  breed: "Airedale",
  age: 3
}

printDog(ginger) // excess properties are OK!

printDog({ breed: "Airedale", age: 3 }) // ERRORS

/*
  Argument of type '{ breed: string; age: number }' is not assignable to parameter of type 'Dog'.
  Object literal may only specify known properties, and 'age' does not exist in type 'Dog'.
*/

// To get rid of above problem, you can define interface with extra proprty with string index signature

interface Dog {
  breed: string
  [propName: string]: any
}

```

## Interface vs Type

Unlike an **interface** declaration, which always introduces a named `object` type, a **type** alias declaration can introduce a name for any kind of type, including `primitive`, `union`, and `intersection` types. With examples, you can find some in-depth difference between `interface` and `type`.

- `Objects / Functions`

  Both can be used to describe the shape of an object or a function signature. But the syntax differs.

  **Interface**

  ```ts
    interface Point {
      x: number
      y: number
    }

    interface SetPoint {
      (x: number, y: number): void
    }
  ```

  **Type alias**
  
  ```ts
  type Point = {
    x: number
    y: number
  }

  type SetPoint = (x: number, y: number) => void
  ```

- `Other Types`

  Unlike an interface, the type alias can also be used for other types such as `primitives`, `unions`, and `tuples` (Aforementioned).

  ```ts
  // primitive
  type Name = string

  // object
  type PartialPointX = { x: number }
  type PartialPointY = { y: number }

  // union
  type PartialPoint = PartialPointX | PartialPointY

  // tuple
  type Data = [number, string]

  ```

- Extend

  Both can be extended, but again, the syntax differs. Additionally, note that an interface and type alias are not mutually exclusive. An interface can extend a type alias, and vice versa.

  **interface extends interface**

  ```ts
    interface PartialPointX { x: number }
    interface Point extends PartialPointX { y: number }
  ```

  **type alias extends type alias**

  ```ts
    type PartialPointX = { x: number }
    type Point = PartialPointX & { y: number }
  ```

  **interface extends type alias**

  ```ts
    type PartialPointX = { x: number }
    interface Point extends PartialPointX { y: number }
  ```

  **type alias extends interface**

  ```ts
    interface PartialPointX { x: number }
    type Point = PartialPointX & { y: number }
  ```

- `Implements`

  A class can implement an interface or type alias, both in the same exact way. Note however that a class and interface are considered static blueprints. Therefore, they can not `implement / extend` a type alias that names a **union type**.

  ```ts
  interface Point {
    x: number
    y: number
  }

  class SomePoint implements Point {
    x = 1
    y = 2
  }

  type Point2 = {
    x: number
    y: number
  }

  class SomePoint2 implements Point2 {
    x = 1
    y = 2
  }

  type PartialPoint = { x: number } | { y: number }

  // ERROR: can not implement a union type
  class SomePartialPoint implements PartialPoint {
    x = 1
    y = 2
  }
  /*
  A class can only implement an object type or intersection of object types with statically known members.
  */
  ```

- `Declaration merging`

  Unlike a type alias, an interface can be defined multiple times, and will be treated as a single interface (with members of all declarations being merged).

  ```ts
    // These two declarations become:
    // interface Point { x: number y: number }
    interface Point { x: number }
    interface Point { y: number }

    const point: Point = { x: 1, y: 2 }
  ```

# Mapped Types - Getting Types from Data

## `typeof` / `keyof` Examples

```ts
const data = {
  value: 123,
  text: "text",
  subData: {
    value: false
  }
}

type Data = typeof data // Data = { value: number; text: string; subData: { value: boolean; } }
```

```ts
const data = ["A", "B"] as const
type Data = typeof data[number] // "A" | "B"
```

```ts
const locales = [
  {
    locale: "se",
    language: "Swedish"
  },
  {
    locale: "en",
    language: "English"
  }
] as const

type Locale = typeof locales[number]["locale"]; // "se" | "en"
```

```ts
const currencySymbols = {
  GBP: "£",
  USD: "$",
  EUR: "€"
}
type CurrencySymbol = keyof typeof currencySymbols; // "GBP" | "USD" | "EUR"
```

## `keyof` with Generics and Interfaces Example

**Exampl-1**:

```ts
interface HasPhoneNumber {
  name: string
  phone: number
}

interface HasEmail {
  name: string
  email: string
}

interface CommunicationMethods {
  email: HasEmail
  phone: HasPhoneNumber
  fax: { fax: number }
}

function contact<K extends keyof CommunicationMethods>(
  method: K,
  contact: CommunicationMethods[K] // turning key into value - a mapped type
) {
  // do something...
}

contact("email", { name: "foo", email: "mike@example.com" })
contact("phone", { name: "foo", phone: 3213332222 })
contact("fax", { fax: 1231 })

// // we can get all values by mapping through all keys
type AllCommKeys = keyof CommunicationMethods
type AllCommValues = CommunicationMethods[keyof CommunicationMethods]
```

**Exampl-2**:

Let's take a `prop` function

```ts
function prop<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]
}

const todo = {
  id: 1,
  text: "Buy milk",
  due: new Date(2016, 11, 31)
};

const id = prop(todo, "id")     // number
const text = prop(todo, "text") // string
const due = prop(todo, "due")   // Date

```

[Article Link](https://mariusschulz.com/blog/keyof-and-lookup-types-in-typescript)

</article>

**[Original and Inspiration](https://github.com/David-Else/modern-typescript-with-examples-cheat-sheet)** by @David-Else

**[MIT-LICENSE](./LICENSE)**