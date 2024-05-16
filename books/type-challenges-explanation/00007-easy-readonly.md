---
title: "Readonly"
---

## task

> Implement the built-in Readonly<T> generic without using it.
> Constructs a type with all properties of T set to readonly, meaning the properties of the constructed type cannot be reassigned.
> For example:

```ts
interface Todo {
  title: string;
  description: string;
}

const todo: MyReadonly<Todo> = {
  title: "Hey",
  description: "foobar",
};

todo.title = "Hello"; // Error: cannot reassign a readonly property
todo.description = "barFoo"; // Error: cannot reassign a readonly property
```

## solution

今回も前回と同じく、組み込み型と同じものを実装しようという課題だ。今回は`Readonly`[^readonly]を作っていこう。

[^readonly]: [Readonly<T> \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/utility-types/readonly)

`T`をすべて`readonly`[^readonly-property]にすればよいだけなので、前回の知識を使えば簡単に実装できるはずだ！

[^readonly-property]: [オブジェクトの型の readonly プロパティ \(readonly property\) \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/values-types-variables/object/readonly-property)

```ts
type MyReadonly<T> = any;
```

左辺はそのまま使えそうなので、右辺を整えていこう。まずやるべきことはプロパティを`readonly`にすることだ。

```ts
type MyReadonly<T> = {
  readonly ...
}
```

あとはもとの`T`からプロパティを取り出せばいい。前回使った知識を使えば簡単にできるはずだ。

```ts
type MyReadonly<T> = {
  readonly [P in keyof T]: T[P];
};
```

今回はあっという間にできてしまった！さぁ、次の課題に進もう！
