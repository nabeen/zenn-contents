---
title: "Concat"
---

## task

[type\-challenges/questions/00533\-easy\-concat/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00533-easy-concat/README.md)

> Implement the JavaScript Array.concat function in the type system. A type takes the two arguments. The output should be a new array that includes inputs in ltr order
> For example:

```ts
type Result = Concat<[1], [2]>; // expected to be [1, 2]
```

## solution

```ts
type Concat<T, U> = any;
```

```ts
type Concat<T extends Array<any>, U extends Array<any>> = any;
```

```ts
type Concat<T extends Array<any>, U extends Array<any>> = [...T, ...U];
```

```ts
type Concat<T extends readonly unknown[], U extends readonly unknown[]> = [
  ...T,
  ...U
];
```
