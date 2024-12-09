---
title: "type-challenges の 1 問目を丁寧に解く"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["TypeScript"]
published: true
publication_name: "globis"
---

## はじめに

この記事は、[GLOBIS Advent Calendar 2024](https://qiita.com/advent-calendar/2024/globis) の 7 日目の記事です。

筆者はここ数年は主に TypeScript を使って開発をしているのですが、その型システムを深淵を覗くために、[type-challenges](https://github.com/type-challenges/type-challenges) を（途中まで）解いたことがあります。

主観ですが、Easy の中では一番最初の問題が一番難しかった記憶があります。n 番煎じにはなりますが、今日はこの最初の問題を丁寧に見ていこうと思います。

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

まず最初に、期待されていることを確認します。期待値は、`MyPick`という型を実装することです。`MyPick`は TypeScript 組み込みの`Pick`[^pick]と同じ振る舞いをすればよいということなので、まずは`Pick`のドキュメントを確認するのがいいでしょう。

[^pick]: [Pick<T, Keys> \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/utility-types/pick)

やるべきことはわかったので、一歩ずつこの型を完成させていきます。

```ts
type MyPick<T, K> = any;
```

まず、`T`に注目してみます。`T`は特に制約はなさそうです。

次に、`K`に注目してみます。`K`は`T`のプロパティ名だけを受け付ける必要がありそうです。型引数に制約をつけるにはどうしたらいいでしょうか。

ここで登場するのが、`extends`[^type-parameter-constraint]です。

[^type-parameter-constraint]: [型引数の制約 \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/generics/type-parameter-constraint#%E5%9E%8B%E5%BC%95%E6%95%B0%E3%81%AB%E5%88%B6%E7%B4%84%E3%82%92%E3%81%A4%E3%81%91%E3%82%8B)

```ts
type MyPick<T, K extends ...> = any;
```

では何を`extends`すればいいでしょうか。先程の繰り返しになりますが、`T`のプロパティ名ですね。では`T`のプロパティを列挙したい場合はどうすればいいでしょうか。

次に登場するのが、`keyof`[^keyof]です。`keyof`を使えば、`T`のプロパティ名をユニオン型[^union]で手に入れることができます。

[^keyof]: [keyof 型演算子 \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/keyof-type-operator)
[^union]: [ユニオン型 \(union type\) \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/values-types-variables/union)

```ts
type MyPick<T, K extends keyof T> = any;
```

ここまでで左辺は埋まったので、残りの右辺を考えていきます。

`K`はユニオン型になっているので、これをキー制約として使えれば良さそうです。ではこれを実現するにはどうすればいいかというと、Mapped Types[^mapped-types]を使うことができます。

[^mapped-types]: [Mapped Types \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/mapped-types)

```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: any;
};
```

ここまで来ればあと一息、といった感じです。`K`の要素を`P`として取り出しているので、これを使って`T`から取り出していきます。ここでは、インデックスアクセス型[^indexed-access-types]を使うことでこれが実現できます。

[^indexed-access-types]: [インデックスアクセス型 \(indexed access types\) \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/type-reuse/indexed-access-types)

```ts
type MyPick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```

これで完成です。

Easy の 1 問目ですが、`extends`に`keyof`、ユニオン型、Mapped Types、インデックスアクセス型と、1 問目にしては多くの概念が登場しました。実際の業務において自前で複雑な型を実装する機会はさほど多くないかもしれませんが、書けたり読めたりして損はないと思うので、コツコツと頑張っていきたいものです。

Happy Coding！ :)

## 採用情報

ご興味ある方はこちらから！

https://recruiting-tech-globis.wraptas.site/
