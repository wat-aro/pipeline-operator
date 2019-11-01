## パイプライン演算子

wat-aro

***

### パイプライン演算子

こんな感じのやつ

```elm
  [1, 2, 3]
  |> List.map (\x -> x * 2)
  |> List.foldl (+)
```

---

#### パイプライン演算子

`|>` の前の値を関数に適用する

---

#### パイプライン演算子

- 元は Isabela/ML あたりが発祥
- F# が採用
- OCaml が採用
- Elixir が F# から採用
- Ruby に入りそう

***
***
### MLなどにおけるパイプライン演算子のつくりかた

以下を満たせば簡単につくれる

- ユーザ定義演算子
- デフォルトでカリー化

---

#### Haskell でのつくり方

第一引数を第二引数に適用するだけ

```haskell
(|>) :: a -> (a -> b) -> b
(|>) a f = f a
```
***
***

### カリー化と部分適用

以降の話に関わるのでここでおさらい

---

#### カリー化

複数の引数を受け取る関数を一引数の関数に変換する

```haskell
uncuffyFunction :: (a, b, c) -> d
uncuffyFunction (a, b, c) = somethingToDo a b c

curriedFunction :: a -> b -> c -> d
curriedFunction a b c = somethingToDo a b c
```

---

#### ES6

```js
const uncurryFunction = (a, b, c) => somethigTodo(a, b, c)

const curriedFunction = a => b => c => somethingTodo(a, b, c)
```

curriedFunctionは引数を一つとると残りの引数を *一つずつ取る関数* を返す
uncurryな関数をcurriedな関数に変換するのが

---

### 部分適用

複数の引数を取る関数で、一部にだけ引数を適用した状態の関数を返す。

```js
const partialApplied = somethigTodo(1, 2, ?)
```

---

### なぜカリー化と部分適用の話を？

Elixir などのデフォルトでカリー化されていない言語では関数のようにパイプライン演算子を定義できない
e.g.
  - Elixir, Clojure はマクロ
  - Ruby は言語組み込みの機構

***

***

### Elixir のパイプライン演算子

ML系は関数の最後の引数に値を差し込む
Elixir は最初の引数を差し込む

---

#### Elixir の |>

```elixir
[1, [[2], 3]]
  |> List.flatten
  |> Enum.map(fn x -> x * x end)
  |> Enum.reduce(0, fn(x, acc) -> x + acc end)
```

---

#### Elixir の |>

カリー化されていないためASTを変換する必要がある

***

***

### Ruby のパイプライン演算子

パイプラインといいつつただの優先度の低い `.`

---

#### Ruby のパイプライン演算子

```ruby
(1..5).map {|x| x * 2 }

1..5 |> map {|x| x * 2 }
```

のように書ける

***

***

#### Ruby のパイプライン演算子における応用

[![Image from Gyazo](https://i.gyazo.com/4ecb8454c6bfc9d8470cb6c118129d21.png)](https://twitter.com/hanachin_/status/1139406434846695430)()

これの話をしたかった

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
```

`.:` は Ruby 2.7 で入る演算子。
Methodオブジェクトを取り出す。
MethodオブジェクトはほぼProcオブジェクトと同じふるまい。
詳しくはドキュメントを読んでください。

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
  |>>> URI.:parse
```

`URI.parse` でなく `URI.:parse` に注意。
とりだしたMethodオブジェクトを `|>` と `>>` で合成する。
`>>` は Ruby2.6 で入った関数(Proc)合成演算子。
`URI.:parse` で `URI.parse` のMethodオブジェクトを取りだし、
`>>` によって合成している。
つまり
```ruby
URI.parse("https://api.github.com/repos/ruby/ruby ")
```

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
  |>>> URI.:parse
  |>>> Net::HTTP.:get
```

`Net::HTTP.:get` で `Net:HTTP.get` のMethodオブジェクトを取り出す。
```ruby
uri = URI.parse("https://api.github.com/repos/ruby/ruby ")
Net::HTTP.get(uri)
```

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
  |>>> URI.:parse
  |>>> Net::HTTP.:get
  |>>> JSON.:parse
```

ここでも `JSON.:parse` で `JSON.parse` でMethodオブジェクトを取り出す

```ruby
uri = URI.parse("https://api.github.com/repos/ruby/ruby ")
response = Net::HTTP.get(uri)
JSON.parse(response)
```

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
  |>>> URI.:parse
  |>>> Net::HTTP.:get
  |>>> JSON.:parse
  |> call
```

今まではMethodオブジェクトを合成しただけなので、ここでやっと評価され、値になる。

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
  |>>> URI.:parse
  |>>> Net::HTTP.:get
  |>>> JSON.:parse
  |> call
  |> fetch("stargazers_count")
```

`Hash#fetch` で値を取りだす

---

#### Ruby のパイプライン演算子における応用

```ruby
"https://api.github.com/repos/ruby/ruby ".:itself
  |>>> URI.:parse
  |>>> Net::HTTP.:get
  |>>> JSON.:parse
  |> call
  |> fetch("stargazers_count")
  |> then { puts @1 }
```

そして標準出力へ。
`@1` は Ruby2.7で入る予定。

```ruby
"hoge".then { puts @1 }
"hoge".then {|s| puts s }
```

---

#### Ruby のパイプライン演算子における応用

たーのしー

***

***

### まとめ

パイプライン演算子の説明
Ruby2.7で入る予定のパイプライン演算子とその応用の紹介
関数合成をキメていこう

---

#### 悲しみ

[![Image from Gyazo](https://i.gyazo.com/47e87c4f7f7ad60bb2f657b4ebbf431b.png)](https://twitter.com/hanachin_/status/1139406434846695430)

---

もっと詳しく知りたい人は

[パイプライン演算子の歴史 - まめめも](https://mametter.hatenablog.com/entry/2019/06/15/192311)
[第一引数版パイプライン演算子 - Qiita @cedretaber](https://qiita.com/cedretaber/items/6a3831367439f64756ab)
