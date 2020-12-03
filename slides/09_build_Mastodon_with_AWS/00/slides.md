## 続Mastodonサーバーを立てる

---

### 三時間クオリティ
- 推敲していないので言葉が荒いです
- 初対面の方に誤解を与えてしまうかも

---

### 以前

- 2019/5: AWSに Mastodon サーバを立てる話をした
    - 途中経過のまま、モチベを失う
- AM 3:00: あれから少し強くなったし、またやるかあ

---

### 目標

- クソザコインフラ構成を見直し, terraformでコード化
- ECS(Elastic Container Service)上にアプリコードをのせる

---

### クソザコインフラ構成

- EC2にMastodonのソースビルドをしていた
- DB、Cache Serverなどを全てEC2にインストール

---

### terraformでコード化の動機

- やったことないので、やる
   - Cloud Formationはある
   - 比較とかしたい

- いまどき、IaaCしないインフラって...
  - すいません

---

### ECS上にアプリコードを載せる

- EC2飽きたし...
- もともと、Mastodon は DockerによるInstallationがデフォ
- 去年のような, サーバ用意して自力でやるのはマイナー
- といっても、インフラを見直すなら, DockerFileを直す必要がある

---

### 進捗

- ECSとDockerの連携でAWSのリソース権限関係でハマっておしまい。

---

### 作業内容

- 以下を順番にやっていく
- https://docs.docker.com/engine/context/ecs-integration/

---

### エラー内容

- ロール MastodonDev に対する, 操作 sts:AssumeRole がユーザー hoge がありません。

```
AccessDenied: User: arn:aws:iam::xxxxxxxxxxx:user/hoge is not authorized to perform: sts:AssumeRole on resource: arn:aws:iam::xxxxxxxxxxx:role/MastodonDev
status code: 403, request id: ad30cfe5-b30d-44cd-8a7f-54cf21af1157
```

---

### 設定したが？？

- MastodonDev は ECSへの全アクセス権をもつロール
- hoge（というかグループ）には以下のポリシーを設定した。

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::xxxxxxxxxxx:role/MastodonDev"
    }
  ]
}
```

---

### 頑張ります


