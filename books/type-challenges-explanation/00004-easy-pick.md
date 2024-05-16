---
title: "Pick"
---

## task

[type\-challenges/questions/00004\-easy\-pick/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00004-easy-pick/README.md)

> Implement the built-in Pick<T, K> generic without using it.
> Constructs a type by picking the set of properties K from T
> For example:

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = MyPick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
```

## solution

おっと、いきなり結構難しそうだ。だが心配は要らない。これからじっくりとこの課題に向き合っていく。

では、期待されていることを確認しよう。`MyPick`という型を実装することが求められている。`MyPick`は TypeScript 組み込みの`Pick`[^pick]と同じ振る舞いをすればよい。

[^pick]: [Pick<T, Keys> \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/utility-types/pick)

よし、やるべきことはわかった！一歩ずつこの型を完成させていこう。

```ts
type MyPick<T, K> = any;
```

そうは言っても、僕らは今は TypeScript の型のことはほとんど知らない。何から手を付ければいいだろう？

まず、`T`は特に制約はなさそうだ。一方で`K`はどうだろう。`T`のプロパティ名だけを受け付ける必要がありそうだ。型引数に制約をつけるにはどうしたらいい？`extends`[^type-parameter-constraint]キーワードが使える！

[^type-parameter-constraint]: [型引数の制約 \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/generics/type-parameter-constraint#%E5%9E%8B%E5%BC%95%E6%95%B0%E3%81%AB%E5%88%B6%E7%B4%84%E3%82%92%E3%81%A4%E3%81%91%E3%82%8B)

```ts
type MyPick<T, K extends ...> = any;
```

では何を`extends`すればいいだろう？そう、`T`のプロパティ名だ。`T`のプロパティを列挙したいがどうすればいいだろう？大丈夫、`keyof`[^keyof]がある。`keyof`を使えば、`T`のプロパティ名がユニオン型[^union]で手に入る。

[^keyof]: [keyof 型演算子 \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/keyof-type-operator)
[^union]: [ユニオン型 \(union type\) \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/values-types-variables/union)

```ts
type MyPick<T, K extends keyof T> = any;
```

これで左辺は埋まったので、あとは右辺を考えていこう。`K`はユニオン型になっているので、これをキー制約として使えれば良さそうだ。これには、Mapped Types[^mapped-types]を使うことができる。

[^mapped-types]: [Mapped Types \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/mapped-types)

```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: any;
};
```

よし、あと一息だ。`K`の要素を`P`として取り出しているので、これを使って`T`から取り出そう。ここで、インデックスアクセス型[^indexed-access-types]を使うことができる。

[^indexed-access-types]: [インデックスアクセス型 \(indexed access types\) \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/indexed-access-types)

```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

これで完成だ！最初からいきなり知らないことがたくさん出てきてしまったが、一歩ずつ問題を分解していけば解決できる。この調子で次の課題にも取り組んでいこう！
