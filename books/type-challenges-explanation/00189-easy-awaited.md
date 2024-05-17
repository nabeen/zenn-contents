---
title: "Awaited"
---

## task

[type\-challenges/questions/00189\-easy\-awaited/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00189-easy-awaited/README.md)

> If we have a type which is a wrapped type like Promise, how we can get the type which is inside the wrapped type?
> For example: if we have Promise<ExampleType> how to get ExampleType?

```ts
type ExampleType = Promise<string>;

type Result = MyAwaited<ExampleType>; // string
```

## solution

```ts
type MyAwaited<T> = any;
```

```ts
type MyAwaited<T extends PromiseLike<any>> = any;
```

```ts
type MyAwaited<T extends PromiseLike<any>> = T extends PromiseLike<infer U>
  ? U
  : never;
```

```ts
type MyAwaited<T extends PromiseLike<any>> = T extends PromiseLike<infer U>
  ? U extends PromiseLike<any>
    ? MyAwaited<U>
    : U
  : never;
```
