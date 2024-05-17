---
title: "Tuple to Object"
---

## task

[type\-challenges/questions/00011\-easy\-tuple\-to\-object/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00011-easy-tuple-to-object/README.md)

> Given an array, transform it into an object type and the key/value must be in the provided array.
> For example:

```ts
const tuple = ["tesla", "model 3", "model X", "model Y"] as const;

type result = TupleToObject<typeof tuple>; // expected { 'tesla': 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

## solution

```ts
type TupleToObject<T extends readonly any[]> = any;
```

```ts
type TupleToObject<T extends readonly any[]> = {
  [P in T[number]]: ...
}
```

```ts
type TupleToObject<T extends readonly any[]> = {
  [P in T[number]]: P;
};
```

```ts
type TupleToObject<T extends readonly (string | number | symbol)[]> = {
  [P in T[number]]: P;
};
```

```ts
type TupleToObject<T extends readonly PropertyKey[]> = {
  [P in T[number]]: P;
};
```
