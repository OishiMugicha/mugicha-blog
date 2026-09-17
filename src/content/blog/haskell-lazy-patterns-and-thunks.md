---
title: Haskellのlazy patternは何を遅らせるのか
description: thunkとパターンマッチから、チルダの意味を考える。
date: 2026-09-17
---

Haskellは遅延評価の言語ですが、パターンの前に `~` を付ける、lazy patternという書き方があります。

もともと遅延評価なのに、さらに「lazy」と指定するのはどういうことなのでしょうか。ここでは、計算を保留する **thunk** と、パターンマッチが要求する評価を分けて考えてみます。

以下の例は、`Strict` など評価の扱いを変える拡張を使わない、通常のHaskellを前提にしています。

## thunkは、まだ実行していない計算

例えば、次の式を考えます。

```haskell
let x = sum [1..1000000] :: Integer
in x + x
```

遅延評価を理解するためのモデルでは、`x` は最初から合計値を持つのではなく、「このリストの合計を求める」という未評価の計算を指しています。このような、あとで評価するために保持された計算を **thunk（サンク）** と呼びます。

thunkは、計算の手順と、その計算に必要な値への参照を持つもの、と考えると分かりやすいです。`x + x` の結果を求めるために `x` が必要になると計算が進み、共有されている `x` の評価結果は再利用されます。単に計算を毎回やり直すのとは違うわけです。

ただし、これは動作を理解するためのモデルです。最適化によってthunk自体が作られないこともあり、ソースコードの式一つにつき必ず一つのthunkができる、という意味ではありません。

そもそも結果が必要なければ、計算を進める必要もありません。

```haskell
ignore :: a -> Int
ignore x = 42

-- ignore undefined は 42
```

`undefined` は評価するとエラーになる値です。この例では `x` の値を調べずに `42` を返せるため、エラーになりません。thunkは「時間がたったら実行される予約」ではなく、「必要になったら評価する計算」なんですね。

## パターンマッチは評価を要求する

では、引数をタプルのパターンにするとどうなるでしょうか。

```haskell
ordinary :: (a, b) -> Int
ordinary (x, y) = 42

-- ordinary undefined はエラー
-- ordinary (undefined, undefined) は 42
```

`x` も `y` も使っていませんが、`(x, y)` にマッチするには、引数の外側にタプルのコンストラクタが現れるところまで評価する必要があります。

ここで、`undefined` と `(undefined, undefined)` は違います。前者はタプルの形すら取り出せません。一方、後者は外側がタプルだと分かっていて、中身だけが未評価のままです。

このように、データの外側のコンストラクタが分かる程度まで評価された状態を、**弱頭正規形（WHNF）** と呼びます。タプルの場合、中身まで全部評価する必要はありません。

「Haskellは遅延評価」というのは、何も評価しないということではなく、結果を出すために必要なところまで評価するということです。通常のコンストラクタパターンは、その評価が必要になる場所の一つです。

## `~` は、パターンマッチも遅らせる

ここでlazy patternを使います。

```haskell
lazy :: (a, b) -> Int
lazy ~(x, y) = 42

-- lazy undefined は 42
```

`~(x, y)` は、その場では引数の形を確認せずにマッチを成功させます。`x` や `y` から値が必要になったときに、初めて元の引数とのマッチを行います。このため、lazy patternは **irrefutable pattern（反駁不能パターン）** とも呼ばれます。

thunkとつなげるなら、`x` や `y` は「元の引数にマッチして、対応する成分を取り出す」という計算を保留している、と捉えられます。`~` があるから引数に初めてthunkができるのではなく、**成分を取り出すためのマッチも先送りする**のがポイントです。

もちろん、成分が必要になれば評価されます。

```haskell
firstLazy :: (a, b) -> a
firstLazy ~(x, y) = x

-- firstLazy (7, undefined) は 7
-- firstLazy undefined の結果を求めるとエラー
```

`~` はエラーを消す記号ではありません。必要になるまで、マッチを待たせる記号です。なお、最初の `7` の例は通常の `(x, y)` でも成功します。使わない第2成分を評価しないことと、外側のタプルのマッチを遅らせることは、別の話です。

この規則は [Haskell 2010 Reportのパターンマッチの節](https://www.haskell.org/onlinereport/haskell2010/haskellch3.html#x8-600003.17) に記述されています。

## 違いが役立つ例：再帰的な定義

評価を少し遅らせることで、再帰的な定義が値を返せる場合があります。

例えば、ペアのリストを二つのリストに分ける関数を、`foldr` で書いてみます。

```haskell
splitPairs :: [(a, b)] -> ([a], [b])
splitPairs = foldr step ([], [])
  where
    step (x, y) ~(xs, ys) = (x : xs, y : ys)

-- take 5 (fst (splitPairs [(n, n * 10) | n <- [1..]]))
-- 結果は [1,2,3,4,5]
```

`foldr` の `step` に渡される第2引数は、残りのリストを処理した結果です。`~(xs, ys)` なら、その結果のタプルを調べる前に、現在の要素から `(x : xs, y : ys)` を返せます。

`~` を外して `(xs, ys)` にすると、現在の結果を返すために、残りの処理結果がタプルになるのを待ちます。その残りもさらに次を待つため、この無限リストでは先頭の結果にたどり着けません。

lazy patternは、このように「まだ分解できない値を待たずに、出力の一部を先に作る」場面で効いてきます。

## マッチの失敗も後回しになる

タプル以外に使うときは、マッチしない値にも注意が必要です。

```haskell
constant :: Maybe Int -> Int
constant ~(Just x) = 42

extract :: Maybe Int -> Int
extract ~(Just x) = x

-- constant Nothing は 42
-- extract Nothing の結果を求めるとパターンマッチのエラー
```

`~(Just x)` は、その場で `Just` かどうかを判定しません。`Nothing` でも関数の本体に進み、`x` が必要になってから失敗します。`Just` と `Nothing` を場合分けしたいなら、通常のパターンマッチを使います。

なお、通常のHaskellでは、`let (x, y) = expression` のようなパターン束縛は暗黙に遅延的です。関数の引数にある `(x, y)` と同じタイミングでマッチする、と考えると混乱しやすいところです。

thunkは未評価の計算を保持するもの、lazy patternはパターンマッチのための評価を先送りするもの。この二つを分けると、遅延評価の言語に `~` が用意されている理由が見えてきます。

## 参考

- [Haskell 2010 Report — Pattern Matching](https://www.haskell.org/onlinereport/haskell2010/haskellch3.html#x8-600003.17)
- [A Gentle Introduction to Haskell — Lazy Patterns](https://www.haskell.org/tutorial/patterns.html)
- [GHC User's Guide — Breakpoints and inspecting variables](https://downloads.haskell.org/ghc/latest/docs/users_guide/ghci.html#breakpoints-and-inspecting-variables)（thunkと、評価せずに値を調べる操作について）
