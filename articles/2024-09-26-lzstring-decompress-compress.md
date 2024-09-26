---
title: "LZString の Decompress と Compress を試す"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "vitest", "LZString"]
published: true
publication_name: "globis"
---

Testing Library の `screen.logTestingPlaygroundURL()` を使って生成される URL を踏むと、なぜ HTML が事前に埋め込まれた状態で表示されるのかが気になり、調べてみた。

## 前提

現在関わっているプロダクトのフロントエンドは React の SPA で作成されており、コンポーネントのテストには `vitest` と `@testing-library/react` を使用している（一部では `@storybook/test-runner` も採用しているが、今回の記事の主題ではないため詳細には触れない）。

`vitest` と `@testing-library/react` の組み合わせは、速度面でもそこそこ快適で気に入っている。ただし、期待通りの挙動にならない場合、実際にレンダリングされた HTML を確認したくなることがある。そんなときに便利なのが `screen.debug()`[^debug] や `screen.logTestingPlaygroundURL()`[^logTestingPlaygroundURL] で、これにより実際の HTML を確認できる。

[^debug]: [Debugging \| Testing Library](https://testing-library.com/docs/dom-testing-library/api-debugging/#screendebug)
[^logTestingPlaygroundURL]: [Debugging \| Testing Library](https://testing-library.com/docs/dom-testing-library/api-debugging/#screenlogtestingplaygroundurl)

例えば、`vite` で作成した React プロジェクトのコードは以下のようになる。これをテスト対象として、それぞれの結果を確認していく。

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

次に、このコンポーネントをテストコードで `render` し、それぞれの結果を見てみる。

```tsx
describe("App", () => {
  it("renders the App component", async () => {
    render(<App />);

    screen.debug();
    screen.logTestingPlaygroundURL();
  });
});
```

`screen.debug()`の出力は以下のように表示される。これは HTML がそのまま出力されるため、要素の操作後にどのような HTML が生成されているのか簡単に確認できる。

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

一方、`screen.logTestingPlaygroundURL()`の出力では、Testing Playground の URL が表示される。この URL を開くことで、どのような HTML がレンダリングされているのかをインタラクティブに確認できる。

```text
https://testing-playground.com/#markup=DwEwlgbgfKkwhgAgBYCcCmAzAvAImQC4EAOAzgFwD0lEYB6AVqQHQjoS6IHyoDm6BPAH0ARgBt4AOwDWuGGAC2vRKVQBjPDTrpmpCL05qJpUnjEB7Xuc7wxg3ADVtiC1bnBK8BCgw58RMipKDHg1AlZ2Tm4+AWFxKVl5JRV1TVU1TxMBUmD0UPC9A0QjeBMzS3NEELCbOzwAJTywlwr3TxhKcGhgZABGKCd6RABqREb8jz6YLuLjU1w1HhB3EQBXInNJKDVzVckCRDBSRAAGDzWNreBiKABRcAPgHbYodMoAQWJiZgJSAA8PM90FBEFIQCp4BB0FxKvRSAcABIAWXqHhuHi6MGIs1K8xCIAAtARkOgCSBzGpSHIAMJiMBqaSITZcEmIQbQsFjJoHVzmY4ESpiPKoSSIBTmDBojqYoA
```

Testing Playground は、`screen.debug()`よりもインタラクティブな操作が可能で、例えば特定の要素をどのように取得すればよいかをサジェストしてくれる。例えばボタンを操作したい場合、以下のように取得すればよいことがわかる。

```tsx
screen.getByRole("button", { name: /count is 0/i });
```

## Decompress

ここで本題に入る。この URL に含まれているパラメータは何を意味しているのか、という話だ。

```text
DwEwlgbgfKkwhgAgBYCcCmAzAvAImQC4EAOAzgFwD0lEYB6AVqQHQjoS6IHyoDm6BPAH0ARgBt4AOwDWuGGAC2vRKVQBjPDTrpmpCL05qJpUnjEB7Xuc7wxg3ADVtiC1bnBK8BCgw58RMipKDHg1AlZ2Tm4+AWFxKVl5JRV1TVU1TxMBUmD0UPC9A0QjeBMzS3NEELCbOzwAJTywlwr3TxhKcGhgZABGKCd6RABqREb8jz6YLuLjU1w1HhB3EQBXInNJKDVzVckCRDBSRAAGDzWNreBiKABRcAPgHbYodMoAQWJiZgJSAA8PM90FBEFIQCp4BB0FxKvRSAcABIAWXqHhuHi6MGIs1K8xCIAAtARkOgCSBzGpSHIAMJiMBqaSITZcEmIQbQsFjJoHVzmY4ESpiPKoSSIBTmDBojqYoA
```

状態を復元できるのだから最終的に HTML にデコードされるはずだが、単純な base64 ではないようだった。思い返せば type-challenges[^type-challenges] も似たような形で TypeScript Playground を立ち上げており、同じ仕組みを使っているところは比較的ありそうである。

[^type-challenges]: [GitHub \- type\-challenges/type\-challenges: Collection of TypeScript type challenges with online judge](https://github.com/type-challenges/type-challenges)

調べてみると、どうやら`LZString`というライブラリを使っているようだったので、オンラインの変換ツールを使って Decompress してみる。

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

Decompress が出来たということは逆に Compress も出来て、任意の HTML を埋め込めるはずなので、それを試していく。手順としては、先ほどと同じく変換ツールを使う。

```text
https://gchq.github.io/CyberChef/#recipe=LZString_Compress('Base64')&input=PGgxPkhhcHB5IENvZGluZyE8L2gxPg
```

変換後の文字列は以下になった。

```text
DwCwjAfAEghgDnAngAgMIHsAmBLAdgcwEJgB6cCIA===
```

これを Testing Playground のパラメータに食わせてやると、無事に任意の URL を埋め込んだ状態の Testing Playground を立ち上げることが出来る。

```html
https://testing-playground.com/#markup=DwCwjAfAEghgDnAngAgMIHsAmBLAdgcwEJgB6cCIA===
```

Happy Coding!
