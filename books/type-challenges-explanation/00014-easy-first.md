---
title: "First of Array"
---

## task

[type\-challenges/questions/00014\-easy\-first/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00014-easy-first/README.md)

> Implement a generic First<T> that takes an Array T and returns its first element's type.
> For example:

```ts
type arr1 = ["a", "b", "c"];
type arr2 = [3, 2, 1];

type head1 = First<arr1>; // expected to be 'a'
type head2 = First<arr2>; // expected to be 3
```

## solution

```ts
type First<T extends any[]> = any;
```

```ts
type First<T extends any[]> = T[0];
```

```ts
type First<T extends any[]> = T[number] extends never ? never : T[0];
```

[配列から全要素の型を生成する \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/tips/generates-type-of-element-from-array)
