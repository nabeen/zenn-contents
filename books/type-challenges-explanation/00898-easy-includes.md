---
title: "Concat"
---

## task

[type\-challenges/questions/00898\-easy\-includes/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00898-easy-includes/README.md)

> Implement the JavaScript Array.includes function in the type system. A type takes the two arguments. The output should be a boolean true or false.
> For example:

```ts
type isPillarMen = Includes<["Kars", "Esidisi", "Wamuu", "Santana"], "Dio">; // expected to be `false`
```

## solution

```ts
type Includes<T extends readonly any[], U> = any;
```

```ts
type Includes<T extends readonly any[], U> = T extends [infer F, ...infer R]
  ? ...
  : ...
```

```ts
type Includes<T extends readonly any[], U> = T extends [infer F, ...infer R]
  ? U extends F
    ? true
    : Includes<R, U>
  : false;
```

```ts
type IsEqual<T, U> = (<G>() => G extends T ? 1 : 2) extends <G>() => G extends U
  ? 1
  : 2
  ? true
  : false;

type Includes<T extends readonly any[], U> = T extends [infer F, ...infer R]
  ? IsEqual<U, F> extends true
    ? true
    : Includes<R, U>
  : false;
```
