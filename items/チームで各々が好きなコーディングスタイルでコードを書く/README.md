# 何が出来るようになるの？

例えば。

gitにコミットされているファイル

```csharp
public void Hoge(int a, int b)
{
    Console.WriteLine("{0}, {1}", a, b);
}
```

Aさんがpullしてくる

```csharp
public void Hoge(int a, int b)
{
    Console.WriteLine("{0}, {1}", a, b);
}
```

Bさんがpullしてくる

```csharp
public void Hoge( int a, int b ) {
  Console.WriteLine( "{0}, {1}" , a , b );
}
```

このように、各々が好きなコーディングスタイル上でコーディングすることが出来るようになります。
（機械的に変換できる箇所のみですが。）
そして、commitした時にはチーム全員の共通のフォーマットになります。

# どうやるの？

gitの“clean”, “smudge”という機能を使います。
これは、checkoutしてきた時およびcommitした時に、何かしらの変更を加えられる便利なやつです。
git diffとかもちゃんと良い感じにでます。
設定手順を細かく丁寧に書く予定は無いので、設定方法はググってください。
https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA-Git-%E3%81%AE%E5%B1%9E%E6%80%A7

# うちのチームでやっている方法

チーム統一のコーディングスタイルを記述した設定ファイルをリポジトリ直下に置き、
各個人の好きな設定ファイルをホームディレクトリに置いておきます。

```
[filter "cheese"]
  smudge = "rand=$RANDOM && cat /dev/stdin > ~/.hoge.tmp.${rand} && コーディングスタイル変えるコマンド ~/.hoge.tmp.${rand} ~/.hoge.option && cat ~/.hoge.tmp.${rand} && rm ~/.hoge.tmp.${rand}"
  clean = "rand=$RANDOM && cat /dev/stdin > ~/.hoge.tmp.${rand} && コーディングスタイル変えるコマンド ~/.hoge.tmp.${rand} `git rev-parse --show-toplevel`/.hoge.option && cat ~/.hoge.tmp.${rand} && rm ~/.hoge.tmp.${rand}"
```

後はこんな感じで、smudgeとclean時にコーディングスタイルを変えてやります。
コーディングスタイル変えるコマンドは、astyleなど使ってる言語毎に良い感じの物があると思うので、よしなにしてください。

# 余談

C#のコーディングスタイル変えるツールでMacでも動く良い物が無かったのでコア部分をBridge.NRefactoryから引っ張ってきて皮だけ作りました。
これもそのうち公開できたらな〜と思ってます。
