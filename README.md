<p align="center"><img src="./typescript.png" width="64" height="64" alt="header-image"></p>
<h2 align="center">Easy cheatsheet to learn Modern Typescript</h2>

<p align="center"
  <a href="https://github.com/mudassar045/typescript-cheatsheet">
  <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs Welcome">
  </a>
</p>

# Table of Content

<article class="markdown-body">

- [Typing Objects](#typing-objects)
  - [`Object` vs `object`](#object-vs-object)

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

</article>

**[Original and Inspiration](https://github.com/David-Else/modern-typescript-with-examples-cheat-sheet)** by @David-Else

**[MIT-LICENSE](./LICENSE)**