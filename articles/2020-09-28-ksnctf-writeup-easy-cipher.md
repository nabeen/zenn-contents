---
title: "ksnctf writeup - Easy Cipher"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CTF"]
published: true
---

## 問題文

[ksnctf \- 2 Easy Cipher](https://ksnctf.sweetduet.info/problem/2)

> EBG KVVV vf n fvzcyr yrggre fhofgvghgvba pvcure gung ercynprf n yrggre jvgu gur yrggre KVVV yrggref nsgre vg va gur nycunorg. EBG KVVV vf na rknzcyr bs gur Pnrfne pvcure, qrirybcrq va napvrag Ebzr. Synt vf SYNTFjmtkOWFNZdjkkNH. Vafreg na haqrefpber vzzrqvngryl nsgre SYNT.

## 解答

高度に訓練された（？）人類なら、なんとなくシーザー暗号っぽいな？と察することができるはずなので、シーザー暗号だと仮定して復号してみます。スクラッチで書いてもいいですが、Python は組み込みで用意されているので、それを使うと楽です。

スクラッチで書く方は、がんばって 13 文字ズラしてください。

```py
import codecs
str = "EBG KVVV vf n fvzcyr yrggre fhofgvghgvba pvcure gung ercynprf n yrggre jvgu gur yrggre KVVV yrggref nsgre vg va gur nycunorg. EBG KVVV vf na rknzcyr bs gur Pnrfne pvcure, qrirybcrq va napvrag Ebzr. Synt vf SYNTFjmtkOWFNZdjkkNH. Vafreg na haqrefpber vzzrqvngryl nsgre SYNT."
print(codecs.decode(str, 'rot13'))
```

出力結果がこちら。

> ROT XIII is a simple letter substitution cipher that replaces a letter with the letter XIII letters after it in the alphabet. ROT XIII is an example of the Caesar cipher, developed in ancient Rome. Flag is FLAGSwzgxBJSAMqwxxAU. Insert an underscore immediately after FLAG.

FLAG が浮き出てきましたね。シーザー暗号で合っていたようです。
