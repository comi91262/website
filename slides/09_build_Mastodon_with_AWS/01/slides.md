## 続続Mastodonサーバーを立てる

---

## 前々回

- https://k-slide.netlify.app/slides/01_setup_aws_with_mastodon/

---

## 前回

- 目標
  - クソザコインフラ構成を見直し, terraformでコード化
  - ECS(Elastic Container Service)上にアプリコードをのせる
- 結果
  - ECSとDockerの連携でAWSのリソース権限関係でハマっておしまい。

---

### 今回

- やってみたこと
  - 自PC上でコンテナベースMastdon Serverを構築する
- 結果
  - 建てたサーバの証明書を発行しようとするあたりで朝に

---

### AWSといっているが...

- そもそも、完成形をちゃんとわかっているの？
- 一足飛びにやらずにまずはローカル(自PC)で立ち上げてみよう！

---

### 参考URL

- Dockerで雑にMastodonを起動する方法
- https://qiita.com/zembutsu/items/fd52a504321dd5d6f0b8
- さくらのとある人の記事
- しかし...

---

### 三年も前の記事なので、古い

- 公式は当時でこんな感じ

```
公式READMEでは、Docker関連の説明で一部足りない箇所があると思い、再整理しました
```

---

### 今日、ハマったところを共有します

---

### レポジトリをとってくる

```
git clone  https://github.com/tootsuite/mastodon.git
cd mastodon
```

---

### 環境設定ファイルの準備

```
mv .env.production.sample .env.production
```

---

### APIトークンの発行

- ふーん、程度で

```
docker-compose run --rm web rake secret
```

---

### .env.production の編集

```
# Federation
LOCAL_DOMAIN=localhost
LOCAL_HTTPS=false  # <- 伏線

PAPERCLIP_SECRET=xxx...　<- 先ほどのAPIトークン
SECRET_KEY_BASE=yyy...　<- 先ほどのAPIトークン
OTP_SECRET=zzz...　<- 先ほどのAPIトークン

```

---

### DB(テーブル) 初期化

- 以下引用

```
docker-compose run --rm web rails db:migrate
特にエラーがでなければ準備完了です。
```

---

### 特にエラーがでなければ

```
rails aborted!
PG::ConnectionBad: could not connect to server: No such file or directory
Is the server running locally and accepting
connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?
```

---

### DBコンテナ側でDBが作られていなかったらしい

- 初期化のために一時的なコンテナを作る。

```
docker run -d --name bootstrap-postgres -e POSTGRES_USER=mastodon -e POSTGRES_PASSWORD=password -e POSTGRES_DB=mastodon_production -v ${PWD}/postgres:/var/lib/postgresql/data postgres:9.6-alpine
```

---

### .env.production の 編集も忘れずに... (1敗)

```
# PostgreSQL
# ----------
DB_HOST=db
DB_USER=mastodon
DB_NAME=mastodon_production
DB_PASS=password
DB_PORT=5432
```

---

### できたので、アクセス

- あれ？

```
% curl http://localhost:3000
<html><body>You are being <a href="https://localhost/">redirected</a>.</body></html>%
```

---


### 伏線: LOCAL_HTTPS=false

- HTTPでも通信できるようにしてくれる設定
- 昔はあったが、今は無くなりました

---

### HTTPSって...

- サーバ証明書発行するの??
- ドメインはどうするの??  買うの???
- オレオレ証明書作っちゃう???
- そもそも、コンテナに証明書発行ってなに???
- 頭がぐるぐる

---

### 頑張ります

- nginx(リバースプロキシ) + docker をよくみる
  - Let's Encript
  - Macの構成はいじりたくないなあ

