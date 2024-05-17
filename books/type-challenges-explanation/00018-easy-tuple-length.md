---
title: "Length of Tuple"
---

## task

[type\-challenges/questions/00018\-easy\-tuple\-length/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00018-easy-tuple-length/README.md)

> For given a tuple, you need create a generic Length, pick the length of the tuple
> For example:

```ts
type tesla = ["tesla", "model 3", "model X", "model Y"];
type spaceX = [
  "FALCON 9",
  "FALCON HEAVY",
  "DRAGON",
  "STARSHIP",
  "HUMAN SPACEFLIGHT"
];

type teslaLength = Length<tesla>; // expected 4
type spaceXLength = Length<spaceX>; // expected 5
```

## solution

```ts
type Length<T> = any;
```

```ts
type Length<T extends readonly any[]> = any;
```

```ts
type Length<T extends readonly any[]> = T["length"];
```
