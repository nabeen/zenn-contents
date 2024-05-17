---
title: "Exclude"
---

## task

[type\-challenges/questions/00043\-easy\-exclude/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00043-easy-exclude/README.md)

> Implement the built-in Exclude<T, U>
> Exclude from T those types that are assignable to U
> For example:

```ts
type Result = MyExclude<"a" | "b" | "c", "a">; // 'b' | 'c'
```

## solution

```ts
type MyExclude<T, U> = any;
```

```ts
type MyExclude<T, U> = T extends U ? never : T;
```
