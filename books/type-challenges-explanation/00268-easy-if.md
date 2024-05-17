---
title: "If"
---

## task

[type\-challenges/questions/00268\-easy\-if/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00268-easy-if/README.md)

> Implement the util type If<C, T, F> which accepts condition C, a truthy value T, and a falsy value F. C is expected to be either true or false while T and F can be any type.
> For example:

```ts
type A = If<true, "a", "b">; // expected to be 'a'
type B = If<false, "a", "b">; // expected to be 'b'
```

## solution

```ts
type If<C, T, F> = any;
```

```ts
type If<C extends Boolean, T, F> = any;
```

```ts
type If<C extends Boolean, T, F> = C extends true ? T : F;
```
