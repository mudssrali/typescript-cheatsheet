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

</article>

**[Original and Inspiration](https://github.com/David-Else/modern-typescript-with-examples-cheat-sheet)** by @David-Else

**[MIT-LICENSE](./LICENSE)**