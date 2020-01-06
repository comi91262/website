## Blawnで Hallo world をしてみた

---

### Blawn とは
- Pleasant Programming Language
    - もっと人間にとって扱いやすい、自分の言語をつくってみたかった
- 作者は中学生
- U-22プログラミング・コンテスト2019にて経済産業大臣賞を受賞

---

#### Blawn ネットの記事 vs 実際のところ (1/2)

- 型名の記述が一切不要
    - 型推論の実装は（まだ）ない
    - LLVMレベルの型エラーがおこる
- 構文の可読性が高い
    - pythonっぽい
    - ただし、インデントは読み飛ばしてる
- すべての関数/クラスがC++でいうところのテンプレート関数/クラス
    - 未調査
    - 新規性があるとしたらここっぽい

---

#### Blawn ネットの記事 vs 実際のところ (2/2)

- コンパイル速度と実行速度が速い
    - LLVMなので...
    - 実装がミニマムなので...
- メモリが安全
    - 該当の機能はみつからず
    - メモリの安全性を初めて達成したのはRust

---

### コードの例

- Hello World

```
print("Hello World")
```

- 恒等関数

```
function id(x)
  return x
```

---

### Blawnをさわる動機

- プログラミング言語オタク
    - 昔、インタプリタを作っていた
- Contributionのチャンス

---

### 今回のやったこと
- ソースビルドしてHello, worldをした。

---

### ビルドに関して

- バイナリはレポジトリにあるが、Linux用のバイナリしかない
- とりあえずMacで動かしたい
- ソースビルドは作者曰く..

```
やめたほうがいいです。どうしてもやりたければLLVM6.0,bison,flexをインストールしてから 
buildディレクトリで"make"してください。
"make -j8"とかにするとコンパイルが早くなります。
```

---

### よしやろう

---

### 基本知識: ビルドとか、コンパイルとか

- ビルド
    - 成果物をつくること
    - 今回は Blawn
    - 専門用語ではない

- コンパイル
    - ある言語からある言語へ変換すること
    - ソースコードから機械語（バイナリ）へ

---

### 基本知識: 分割コンパイル

- 一つのソースファイルで開発しているとだんだん管理がつらくなっていく
- そのため、いくつかのファイルにソースコードを分割する
- 結果、ビルド時、各ファイルをコンパイルし、まとめる必要がある

---

### 基本知識: ビルドエラーのときあるある

- <なんか.h> に指定された .hファイルが見つからなくてコンパイルエラー
- .a .dylib 等のライブラリにパスが通ってなくて、リンクエラー

---

### MacOS用のバイナリを作る。

- 用意したもの
    - LLVM@6 (コンパイラバックエンド)
    - clang@6 (C/C++コンパイラ)
    - flex (字句解析器自動生成ツール)
    - bison (構文解析器自動生成ツール)
    - make (ビルド実行ツール)

---

### Makefile を書き換える

- CCを書き換え

```
CC := g++ -std=gnu++17 -std=c++17 -lstdc++fs -O0
```
を
```
CC := $(LLVM_BIN_PATH)/clang++ -std=gnu++17 -std=c++17 -O0
```
に

---

### Makefile を書き換える

- LLVM_LINK_OPTION を書き換え

```
LLVM_LINK_OPTION := `llvm-config --cxxflags --system-libs --ldflags --libs core` -fexceptions -O0 -std=gnu++17 -std=c++17 -lstdc++fs
```
を
```
LLVM_LINK_OPTION := `$(LLVM_BIN_PATH)/llvm-config --cxxflags --system-libs --ldflags --libs all` -fexceptions -O0 -std=gnu++17 -std=c++17 -lc++experimental
```
に

---

### そして

---

### できた？

- ビルドログ抜粋

```
/usr/local/opt/llvm@6/bin/clang++ -std=gnu++17 -std=c++17 -O0 -g -o blawn main.o compiler.o driver.o parser.o lexer.o ast_generator.o \
./node.o builtins.o ir_generator.o utils.o errors.o blawn_context.o -L -L/usr/local/opt/flex/lib \
 -ll `/usr/local/opt/llvm@6/bin/llvm-config --cxxflags --system-libs --ldflags --libs all` \ 
-fexceptions -O0 -std=gnu++17 -std=c++17 -lc++experimental
cp blawn ../../blawn/blawn
cp -r ../tools ../../blawn/tools
```

---

### hello.blawn

```
print("Hello, World")
```

---

### コンパイルしてみる

- blawn はコンパイルかつ実行もしてくれる
- 実行すれば、Hello, World が表示される

```
# ./blawn hello.blawn
```

---

### いけえええええ

```
xxx:~/Blawn/src/build (master *>) $ ./blawn hello.blawn
sh: /Users/xxx/Blawn/src/./data/llc: No such file or directory
```

---

🤔

---

### llc?  その前にBlawnのコンパイラ過程

- ソースコード -> 抽象構文木
    - 字句解析、構文解析
- 抽象構文木 -> LLVM IR
- LLVM IR -> アセンブラ言語
- アセンブラ言語 -> 機械語

---

### llc って
- LLVM IR を アセンブラのコードに変換するコンパイラ（トランスパイラ）

---

### src/compiler/compiler/compiler.cpp

- 謎のパスが指定されていたので、とりあえず直した。
- そもそものカレントパスが親ディレクトリだった...

```
std::vector<std::string> commands;
std::string cmd1 = abs("./data/llc") + " -O3 " + ir_filename + " -o " + abs("tmp/result.s");
```
を
```
std::string cmd1 = std::string("/usr/local/opt/llvm@6/bin/llc -O3 " + ir_filename + " -o " + "tmp/result.s");
```
に

---

### これでいけるはず！

```
xxx:~/Blawn/src/build (master *>) $ ./blawn hello.blawn
Undefined symbols for architecture x86_64:
  "get_blawn_context()", referenced from:
      builtins::load_builtin_functions(llvm::LLVMContext&, llvm::Module&, llvm::IRBuilder<llvm::ConstantFolder,
      中略
ld: symbol(s) not found for architecture x86_64
clang-6.0: error: linker command failed with exit code 1 (use -v to see invocation)
compilation failed.
....Don't mind!
```

---

🤔

---

### 原因

- 組み込み関数をビルドしたオブジェクトファイルは C++版 と C版があり、C版を使う必要があった。

```
std::string cmd = "/usr/local/opt/llvm@6/bin/clang" + arguments + " tmp/result.s builtins.o" + " -o " + output_filename;
```
を
```
std::string cmd = "/usr/local/opt/llvm@6/bin/clang" + arguments + " tmp/result.s builtins_c.o" + " -o " + output_filename;
```
に

---

### これでいける！！！

```
xxx:~/Blawn/src/build (master *>) $ ./blawn hello.blawn
xxx:~/Blawn/src/build (master *>) $
```

---

🤔

---

### 原因

- 指定した result.s に別の作業でビルドしたものを使っていた
- これのせいで、アセンブラの海を潜るはめに...

```
std::string cmd = "/usr/local/opt/llvm@6/bin/clang" + arguments 
 + " tmp/result.s builtins_c.o" + " -o " + output_filename;
```

---

### ようやく

```
xxx:~/Blawn/src/build (master *>) $ ./blawn hello.blawn
Hello, World
xxx:~/Blawn/src/build (master *>) $
```
