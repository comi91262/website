## Goをはじめた

---

### Goとは

- プログラミング言語
- Google発
- コンパイル言語
- 軽量スレッドによる並行計算

---

### Goを学ぶ動機

- DevOps Roadmapによる学ぶべき言語
  - もうGoしか残ってなかった
- 昔流行ってた
  - 今はよくわからない
  - そろそろ重い腰をあげたい

---

### 今月やったこと

- ドキュメントを読んだ
- HackerRankの問題を解いた
- DiscordのBotを作った

---

### ドキュメントを読んだ

- https://golang.org/doc/
- たまには愚直に読んでいく
  - 自分の血肉にするつもりでやる

---

### どこまで読んだか

- Installing Go
  - Getting Started 
- Learning Go 
  - A Tour of Go
- How to write Go code
  - プロジェクトの作り方(今回は説明しない)
- Editor plugins and IDEs 
  - おすすめのIDE（今回は説明しない)
- Effective Go (途中)
  - Effective Java的なあれ（今回は説明しない)

---

###  Installing Go

>>>

### Install the Go Tools

- pkgファイルをダウンロードし、インストールした
- ```/usr/local/go``` にインストールされる
  - golang/go にあるものと同じものが

>>>

### Test your installation

```
// Goのプログラムはpackageで構成される
// Goのプログラムはmainパッケージから実行される
package main

// Cっぽい、IO関数を提供するライブラリ
import "fmt"

func main() {
	fmt.Printf("hello, world\n")
}
```

>>>

## 実行結果

```
username:~/go-work $ vim hello.go
username:~/go-work $ go build hello.go
username:~/go-work $ ls
hello		hello.go
username:~/go-work $ file hello
hello: Mach-O 64-bit executable x86_64
username:~/go-work $ ./hello
hello, world
```

---

## A Tour of Go


- https://tour.golang.org/welcome/1
- 3つのセクション
  - 基本的な文法とデータ構造
  - メソッドとインターフェイス
  - 並行処理の機能

---

### どのようにやったか

- 有志による日本語版をやった
  - ローカルは対応してない
  - 時間がないので、これを使う

- たまに日本語訳がおかしいので注意
> Functions are values too. They can be passed around just like other values.
> 関数も変数です。他の変数のように関数を渡すことができます。

---

### 特徴的な言語仕様を紹介する

- 従来の言語を洗練させた構文要素
- チーム開発で使うとまずい構文要素
- 並行プログラミングのための仕様は省略
  - 私があまり慣れていないため
- (当然,) 包括的ではない

---

- 型の書き方がきもすぎる
  - TODO: なぜこういう書き方にしたのか調べる
```
 //  a: int と書きたい
  var a int = 3
 // b: int[3] でしょ、普通
  var b [3]int = {1, 2, 3}
```


>>>

- Named return values
  - https://go-tour-jp.appspot.com/basics/7

>>>

- rune
  - 文字型
  - alias for int32
  - represents a Unicode code point
  - 厨二病じゃん

>>>

- forが洗練されている
https://tour.golang.org/flowcontrol/1

>>>

- switch がまあまあ強力
https://tour.golang.org/flowcontrol/9

>>>

- defer がやばそう
https://go-tour-jp.appspot.com/flowcontrol/12

>>>

- ポインタがある
  - パフォーマンスのよいコードがかけそう
    - ポインタによる参照渡しがマニュアルでできる
  - ポインタ演算はない（安心）

>>>

- nil pointer dereference がある
  - いわゆる ぬるぽ (cf. Javaの１０億ドルの誤り）
  - TODO: なぜこれをいれたのか、調べる

---

## HackerRankの問題を解いた

---

### HackerRankって？

- AtCoderみたいなの
  - 問題が解けたり...
  - コンテストが開かれたり...
  - 就職で使えたり...

- 興味のある方は #z_hacker_rank へ

---

### 解いた問題

- DIFFICULTY: Easy
- SUBDOMAINS: Warmup, Implementation
- 34問

---

### snippet を眺めてみよう

- snippet: 問題を解く中で得たコード
  - 流用が目的

---

### 開発環境を整える

- vim
- plugin vim-go
  - syntax highlight, lint, format, compile

---

### それでは、鑑賞会です

>>>

- 入力を整数値にする
  - Go には例外がない（最高）

```
func nextInt() int {
	sc.Scan()
	i, e := strconv.Atoi(sc.Text())
	if e != nil {
		panic(e)
	}
	return i
}
```

>>>

- 絶対値
  - mathは基本的にfloat64なのでつらい
```
fmt.Println(math.Abs(float64(a - b)))
```

>>>

- 小数出力
  - C like

```
fmt.Printf("%.6f\n", x)
```

>>>

- float型の値が整数かどうか
  - なんと、用意されていない...

```
func isInt(val float64) bool {
    return val == float64(int(val))
}
```

>>>

- 最大公約数を求める

```
func gcd(a, b int) int {
    if b == 0 {
        return a
    }
    return gcd(b, a%b)
}
```

>>>

- 整数値の列挙
  - これ効率悪いらしい
  - TODO: append が遅い

```
func enumarate(n int, m int) []int {
    var arr []int
    for i := n; i < m; i++ {
        arr = append(arr, i)
    }
    return arr
}
```

>>>

- 時刻をパースする
https://blog.toshimaru.net/go-time-format/#2006%E5%B9%B41%E6%9C%882%E6%97%A5%E3%81%AE%E8%AC%8E

```
 layout := "Jan 2, 2006 at 3:04pm (MST)"
 value := "Feb 3, 2013 at 7:54pm (PST)"
 t, _ := time.Parse(layout, value)
```

---

## DiscordのBotを作った

---

### Discord?

- Skypeみたいにビデオ通話ができるツール
- ゲーム特化

---

### 何作ったの?

- スプラトゥーン2のランダムブキbot
  - 入力: /buki
  - 出力: {入力した人の名前} << {ブキ名}

- 元々 Pythonで書いていたものをGoで書き直した

---

### システム構成

- 2 -> 3 -> 1 -> 3 -> 2 で処理される（多分)

1. Heroku (Bot プログラム)
2. Discord App (Client)
3. Discord のサーバー

---

### ソースコード

>>>

### 定数とか

```
package main

import (
	"fmt"
	"github.com/bwmarrin/discordgo"
	"log"
	"math/rand"
	"strings"
	"time"
)

var TOKEN = ""

var bukiList = []string{
    // ブキ名
}

var chargerList = []string{
    // ブキ名
}
```

>>>

### main

```
func main() {
	rand.Seed(time.Now().UnixNano())

	discord, err := discordgo.New("Bot " + TOKEN)

	if err != nil {
		log.Println("Error logging in")
	}

	discord.AddHandler(onMessageCreate)

	err = discord.Open()
	if err != nil {
		log.Println(err)
	}

	fmt.Println("Listening...")

    // ロック
	<-make(chan bool)
	return
}
```

>>>

### メッセージを作るところ

```
func onMessageCreate(s *discordgo.Session, m *discordgo.MessageCreate) {
	c, err := s.State.Channel(m.ChannelID) //チャンネル取得
	if err != nil {
		log.Println("Error getting channel: ", err)
		return
	}
	if m.Author.Bot {
		return
	}
	fmt.Printf("%20s %20s %20s > %s\n", m.ChannelID, time.Now().Format(time.Stamp), m.Author.Username, m.Content)

	var userName string
	if m.Member.Nick == "" {
		userName = m.Author.Username
	} else {
		userName = m.Member.Nick
	}

	switch {
	case strings.HasPrefix(m.Content, "/buki"):
		chargerOrElse := rand.Intn(100)
		if chargerOrElse == 50 {
			randomInt := rand.Intn(16)
			msg := userName + " << 大当たり! " + chargerList[randomInt]
			sendMessage(s, c, msg)
		} else {
			randomInt := rand.Intn(123)
			msg := userName + " << " + bukiList[randomInt]
			sendMessage(s, c, msg)
		}
	}
}

func sendMessage(s *discordgo.Session, c *discordgo.Channel, msg string) {
	_, err := s.ChannelMessageSend(c.ID, msg)
	if err != nil {
		log.Println("Error sending message: ", err)
	}
}
```

---

### Herokuへのデプロイ

- 省略
- go.mod ファイルを定義しなければならない
  - package.json, composer.json とかのやつ

---

### 次の予定

- ドキュメントを読み進める
  - Effective Go
  - F&Q, wiki
  - Go Language Specification
  
- TODOを調べる
- 軽量スレッドに関して調べる (DevOps roadMap)




