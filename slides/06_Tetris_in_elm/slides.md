### Elm で テトリスを書いた

---

### Elm (えるむ) とは

- プログラミング言語
- Webフロントエンドに特化
- 純粋関数型
- 言語内フレームワークをもつ

---

### ランタイムエラーがおきない

- ElmはJavaScript (ES5) にコンパイルされる 
- Elmは静的型付き言語
- 変換後のJSもランタイムエラーがおきない

---

### 純粋関数型プログラミング言語

- 長くなるので省略
- Haskell の親戚ぐらいに思ってください

---

### The Elm Architecture (TEA)

- 単一データフローパターン
- Elmの開発者は、このパターンに沿えばうまくいくと思ったらしい
- のち、Redux, Vuexに考え方をパクられる

---

### Elmをさわる動機

- JQuery, React の先へ
- いい加減重い腰をあげる

---

### 何故、テトリスか

- Hello, World はブラウザで完結させたかった
  - サーバとのやりとりはまた今度
- 最初に思いついたものは仕方ない

---

### TEAの基本

Elmのプログラムは以下の３つで構成される

- Model: アプリケーションの状態
- View: 状態をHTMLに変換する方法
- Update: メッセージに基づいて状態を更新する方法

---

### 例

https://elm-lang.org/examples/buttons

---

### コマンドとサブスクリプション

- サーバ通信など, Elmの外の世界とやり取りする仕組み
- コマンド: HTTPリクエストや乱数取得
- サブスクリプション: 現在時刻, ユーザー入力を常に監視

>>>

### 参考: SPA

- コマンドを使って達成できる気がしませんか

---

### 開発環境構築

---

### インストール

最新は 0.19 (2019年)

```
npm install -g elm
```

---

### ツール群

- elm repl
    - いわゆるインタラクティブモード
- elm reactor
    - サーバー起動
    - ファイルのコンパイル
- elm make
    - コンパイルする
- elm install
    - パッケージのインストール

---

### 使用エディタ vim

- plugin ElmCast/elm-vim
  - コード補完
  - 自動フォーマット
  - 他

---

### テトリス

- https://ja.wikipedia.org/wiki/%E3%83%86%E3%83%88%E3%83%AA%E3%82%B9
- テトリミノという単語だけ覚えてください

---

### 先にものをみせます

- https://github.com/ykonomi/elm-tetris
- 518行くらい

---

### Model

```
type alias Model =
    { board : List Tile
    }
```

参考: スコアを実装する場合, フィールドに

```
 , score: Int
```

>>>

### Tile は自前

```
type Tile
    = None  -- 何もなし
    | Wall --  かべ
    | Block -- 固まったテトリミノ
    | Tetrimino Tetrimino -- テトリミノ

type Tetrimino
    = I | O | S | Z | J | L | T
```

---

### Init

```
init : ( Model, Cmd Msg )
init =
    let
        board =
            List.initialize 200 (\n -> None) ++ List.initialize 10 (\n -> Wall)
    in
    ( { board = putTetrimino 4 0 I board }, Cmd.none )
```

---

### View

```
view : Model -> Html Msg
view model =
    layout [] (wrappedRow [ width (px 300) ] (List.map viewTile model.board))
```

---

### Subscriptions

```
subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.batch
        [ Time.every 100 Tick
        , onKeyDown (Decode.map Change keyDecoder)
        ]
```

---

### Update

```
update msg model =
    case msg of
        Tick _ ->
            if canMove model.board Down then
                ( { board = moveTetrimino model.board Down }, Cmd.none )

            else
                ( { board = toBlock model.board |> vanishBlock }, generateBlock )

        Change direction ->
            if canMove model.board direction then
                ( { model | board = moveTetrimino model.board direction }, Cmd.none )

            else
                ( model, Cmd.none )

        NewTetrimino x ->
            ( { board = putTetrimino 4 0 x model.board }, Cmd.none )
```

---

### まとめ

- Elmによるテトリス
- コードが読みやすそうな気がしません?
  - 宣言的でドキュメント性が高い
- コードが書きやすそうな気がしません?
  - 何をどこに書けばいいかがわかる



