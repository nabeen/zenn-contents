---
title: "Hello World"
---

## task

[type\-challenges/questions/00013\-warm\-hello\-world/README\.md at main · type\-challenges/type\-challenges](https://github.com/type-challenges/type-challenges/blob/main/questions/00013-warm-hello-world/README.md)

> Hello, World!
>
> In Type Challenges, we use the type system itself to do the assertion.
>
> For this challenge, you will need to change the following code to make the tests > pass (no type check errors).

```typescript
// expected to be string
type HelloWorld = any;
```

```typescript
// you should make this work
type test = Expect<Equal<HelloWorld, string>>;
```

## solution

Warm Up 問題なので、まずはカンタンなところからだ。

まずは期待値を見てみよう。`Expect`や`Equal`がどういう実装かはわからないが（今後も同様に、不要な部分は読み飛ばしていくつもりだ）、言っていることはわかるだろう。どうやら、`HelloWorld`が`string`と等しいことを期待しているようだ。

幸い TypeScript には型エイリアス[^type-alias]という仕組みがあって、自分で自由に型に名前をつけることができる。やってみよう。

```typescript
type HelloWorld = string;
```

これは、`HelloWorld`という型は`string`だ、と定義している。これで期待値を満たすことができた。

準備運動はこれで終わり、次から一歩ずつ TypeScript の型システムに Deep Dive していこう！

[^type-alias]: [型エイリアス \(type alias\) \| TypeScript 入門『サバイバル TypeScript』](https://typescriptbook.jp/reference/values-types-variables/type-alias)
