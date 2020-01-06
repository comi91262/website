## GraphQLの調査結果発表

---

### 注意

- 3時間クオリティ
- 手を動かしてないので適当なことを言ってるかも
    - いわゆるエアプ

---

### GraphQL とは

- WebAPIのための規格
    - クエリ言語
    - server-side ランタイム 
- FaceBook製 
- 具体例: GitHub API v4

---

### 例: スキーマの定義

- ファイル名の例 scheme.graphqls

```
type Query {   // Queryはrootの型
  currentUser: User!
}

type User {
  id: Int!
  name: String!
}
```

---

### 例: リゾルバを実装

```
// Kotlin code
class Query: GraphQLQueryResolver { // GraphQL Java Tools
  fun currentUser(): User {
     val user = User(1, "hoge") // 本来はDBからUserの情報をとってくる
     return user
  }
}
```

---


### 例: クエリ発行

- 単一のエンドポイント
- JSONでシリアライズ化にしてPOSTするのが簡単なやり方
- クライアントあり (e.g. apollo)
    - キャッシュとかしてくれる

```
{
currentUser {
    id
    name
 }
}
```

---

### 例: レスポンス

```
{
  "data": {
    "currentUser": {
      "id": "1",
      "name": "hoge",
    }
  }
}
```

---

### 長所

- リクエストを見ればレスポンスの構造がわかる（逆も然り）
- リソースを型ありで記述できる
- 複数のリソースを1度のリクエストで取得し、取得処理が煩雑にならない

---

### 短所

- 単一エンドポイントのため、パフォーマンス分析や集計が難しい
- ランタイムの実装はライブラリを使うため、コストがライブラリに依る
- 素朴にリゾルバを実装すると N+1 問題が発生する
- JSONでシリアライズして送る都合、画像や動画の扱いが苦手

---

### RESTful APIとの違い

- エンドポイントが単一
- メソッドが GET/POST/PUT/PATCH だけに縛られない
- ステータスコードは 処理が成功で200 
    - ステータスコードで処理を分岐させない
    - レスポンスのerrorsフィールドでやる

---

### 開発手法

- Schema-First
   - Schemaから作成
   - クライアント、サーバで開発チームが分かれているときに有用
- Code-First
   - Resolverから作成し、Schemaは自動生成する
   - Schemaの定義と実装のズレがなくなる
