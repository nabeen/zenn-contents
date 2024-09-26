---
title: "LZString の Decompress と Compress を試す"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "vitest", "LZString"]
published: true
publication_name: "globis"
---

Testing Library の`screen.logTestingPlaygroundURL()`を使っている時に生成される URL を踏むと、なぜ HTML が埋まった状態で表示されるのかが気になったので調べてみた。

## 前フリ

僕が今携わっているプロダクトのフロントエンドは React の SPA で組んでおり、コンポーネントのテストとしては、`vitest`と`@testing-library/react`を使っている（一部`@storybook/test-runner`を使っている箇所もあるが、今回は本題ではないので言及しない）。

`vitest`と`@testing-library/react`の組み合わせは速度面でも悪くないので好きだが、たまに期待値と異なる挙動になったときに、実際にレンダリングされているものを見たくなったりする。そんな時に便利に使えるのが、`screen.debug()`[^debug]だったり、`screen.logTestingPlaygroundURL()`[^logTestingPlaygroundURL]で、実際の HTML を見ることができる。

[^debug]: [Debugging \| Testing Library](https://testing-library.com/docs/dom-testing-library/api-debugging/#screendebug)
[^logTestingPlaygroundURL]: [Debugging \| Testing Library](https://testing-library.com/docs/dom-testing-library/api-debugging/#screenlogtestingplaygroundurl)

例えば、`vite`で React のプロジェクトを作成した場合、以下のようなコードが生成されるが、これを対象としてそれぞれの結果を見てみる。

```tsx
import { useState } from "react";
import reactLogo from "./assets/react.svg";
import viteLogo from "/vite.svg";
import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <div>
        <a href="https://vitejs.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
        <p>
          Edit <code>src/App.tsx</code> and save to test HMR
        </p>
      </div>
      <p className="read-the-docs">
        Click on the Vite and React logos to learn more
      </p>
    </>
  );
}

export default App;
```

テストコード側で、`render`した後のそれぞれの結果を見てみる。

```tsx
describe("App", () => {
  it("renders the App component", async () => {
    render(<App />);

    screen.debug();
    screen.logTestingPlaygroundURL();
  });
});
```

`screen.debug()`の方は以下のように出力される。HTML がそのまま出力されるので、要素を操作した後に実際にどういう HTML になっているか、等をお手軽に確認できる。

```html
<body>
  <div>
    <div>
      <a href="https://vitejs.dev" target="_blank">
        <img alt="Vite logo" class="logo" src="/vite.svg" />
      </a>
      <a href="https://react.dev" target="_blank">
        <img alt="React logo" class="logo react" src="/src/assets/react.svg" />
      </a>
    </div>
    <h1>Vite + React</h1>
    <div class="card">
      <button>count is 0</button>
      <p>
        Edit
        <code> src/App.tsx </code>
        and save to test HMR
      </p>
    </div>
    <p class="read-the-docs">Click on the Vite and React logos to learn more</p>
  </div>
</body>
```

一方、`screen.logTestingPlaygroundURL()`の出力は以下のようになる。Testing Playground の URL が出力されるので、それを開くことで、実際にどういう HTML になっているかを確認できる。

```text
https://testing-playground.com/#markup=DwEwlgbgfKkwhgAgBYCcCmAzAvAImQC4EAOAzgFwD0lEYB6AVqQHQjoS6IHyoDm6BPAH0ARgBt4AOwDWuGGAC2vRKVQBjPDTrpmpCL05qJpUnjEB7Xuc7wxg3ADVtiC1bnBK8BCgw58RMipKDHg1AlZ2Tm4+AWFxKVl5JRV1TVU1TxMBUmD0UPC9A0QjeBMzS3NEELCbOzwAJTywlwr3TxhKcGhgZABGKCd6RABqREb8jz6YLuLjU1w1HhB3EQBXInNJKDVzVckCRDBSRAAGDzWNreBiKABRcAPgHbYodMoAQWJiZgJSAA8PM90FBEFIQCp4BB0FxKvRSAcABIAWXqHhuHi6MGIs1K8xCIAAtARkOgCSBzGpSHIAMJiMBqaSITZcEmIQbQsFjJoHVzmY4ESpiPKoSSIBTmDBojqYoA
```

Testing Playground は先程の`screen.debug()`よりもリッチで、この要素どうやって取得すればいいんだっけ...？というのも、Testing Playground だとサクッとサジェストしてくれて非常に便利。例えばボタンを押したいなら、以下のように取得すればいいことがわかる。

```tsx
screen.getByRole("button", { name: /count is 0/i });
```

さて、ここでようやく本題に入っていくのだけど、この URL についているこのパラメーターは何者だ、という話。

```text
DwEwlgbgfKkwhgAgBYCcCmAzAvAImQC4EAOAzgFwD0lEYB6AVqQHQjoS6IHyoDm6BPAH0ARgBt4AOwDWuGGAC2vRKVQBjPDTrpmpCL05qJpUnjEB7Xuc7wxg3ADVtiC1bnBK8BCgw58RMipKDHg1AlZ2Tm4+AWFxKVl5JRV1TVU1TxMBUmD0UPC9A0QjeBMzS3NEELCbOzwAJTywlwr3TxhKcGhgZABGKCd6RABqREb8jz6YLuLjU1w1HhB3EQBXInNJKDVzVckCRDBSRAAGDzWNreBiKABRcAPgHbYodMoAQWJiZgJSAA8PM90FBEFIQCp4BB0FxKvRSAcABIAWXqHhuHi6MGIs1K8xCIAAtARkOgCSBzGpSHIAMJiMBqaSITZcEmIQbQsFjJoHVzmY4ESpiPKoSSIBTmDBojqYoA
```

## Decompress

状態を復元できるのだから最終的に HTML にデコードされるはずだが、単純な base64 ではないようだった。思い返せば type-challenges[^type-challenges] も似たような形で TypeScript Playground を立ち上げており、同じ仕組みを使っているところは比較的ありそうである。

[^type-challenges]: [GitHub \- type\-challenges/type\-challenges: Collection of TypeScript type challenges with online judge](https://github.com/type-challenges/type-challenges)

調べてみると、どうやら`lz-string`というライブラリを使っているようだったので、オンラインの変換ツールを使ってデコードしてみる。

```text
https://gchq.github.io/CyberChef/#recipe=LZString_Decompress('Base64')&input=RHdFd2xnYmdmS2t3aGdBZ0JZQ2NDbUF6QXZBSW1RQzRFQU9BemdGd0QwbEVZQjZBVnFRSFFqb1M2SUh5b0RtNkJQQUgwQVJnQnQ0QU93RFd1R0dBQzJ2UktWUUJqUERUcnBtcENMMDVxSnBVbmpFQjdYdWM3d3hnM0FEVnRpQzFibkJLOEJDZ3c1OFJNaXBLREhnMUFsWjJUbTQrQVdGeEtWbDVKUlYxVFZVMVR4TUJVbUQwVVBDOUEwUWplQk16UzNORUVMQ2JPendBSlR5d2x3cjNUeGhLY0doZ1pBQkdLQ2Q2UkFCcVJFYjhqejZZTHVMalUxdzFIaEIzRVFCWEluTkpLRFZ6VmNrQ1JEQlNSQUFHRHpXTnJlQmlLQUJSY0FQZ0hiWW9kTW9BUVdKaVpnSlNBQThQTTkwRkJFRklRQ3A0QkIwRnhLdlJTQWNBQklBV1hxSGh1SGk2TUdJczFLOHhDSUFBdEFSa09nQ1NCekdwU0hJQU1KaU1CcWFTSVRaY0VtSVFiUXNGakpvSFZ6bVk0RVNwaVBLb1NTSUJUbURCb2pxWW9B
```

この結果を見ると、`screen.debug()`で出したものの`body`の中身を復元できていることがわかる。

```html
<div>
  <div>
    <a href="https://vitejs.dev" target="_blank"
      ><img src="/vite.svg" class="logo" alt="Vite logo" /></a
    ><a href="https://react.dev" target="_blank"
      ><img src="/src/assets/react.svg" class="logo react" alt="React logo"
    /></a>
  </div>
  <h1>Vite + React</h1>
  <div class="card">
    <button>count is 0</button>
    <p>Edit <code>src/App.tsx</code> and save to test HMR</p>
  </div>
  <p class="read-the-docs">Click on the Vite and React logos to learn more</p>
</div>
```

## Compress

Decompress が出来たということは逆に Compress もできて、任意の HTML を埋め込むことが出来るので試していく。手順としては、先ほどと同じく変換ツールを使う。

```text
https://gchq.github.io/CyberChef/#recipe=LZString_Compress('Base64')&input=PGgxPkhhcHB5IENvZGluZyE8L2gxPg
```

変換後の文字列が以下。

```text
DwCwjAfAEghgDnAngAgMIHsAmBLAdgcwEJgB6cCIA===
```

これを Testing Playground のパラメータに食わせてやると、無事に任意の URL を埋め込んだ状態の Testing Playground を立ち上げることが出来る。

```html
https://testing-playground.com/#markup=DwCwjAfAEghgDnAngAgMIHsAmBLAdgcwEJgB6cCIA===
```

Happy Coding!
