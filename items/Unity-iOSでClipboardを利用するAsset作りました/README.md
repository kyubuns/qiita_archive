# UniClipboard

native pluginを作る勉強用に作ってみました。
勉強用なので特に拡張する気は無いです。

実は本題は「作ったこと」ではなく、「共有フォルダ(Assets/Editor, Assets/Plugins/iOS)を汚さないAssetを作る方法」「UnityのiOS NativePluginでARCを利用する方法」です。
それは下の方で触れます。

## 何が出来るの？

PC/Mac/iOSでClipboardにTextをset/get出来る。
webplayer, androidは未対応。

## 名前

AssetStoreでUni~~とかNextGen~~とか流行ってるので
流行に乗ってみました。

## ダウンロード

https://github.com/kyubuns/uniclipboard

## サンプルコード

```lang:sample.cs
if(GUI.Button(new Rect(20,40,300,60), "Copy"))
{
	UniClipboard.value = "Hello, UniClipboard!";
}

if(GUI.Button(new Rect(20,120,300,60), "Paste"))
{
	clipboardValue = UniClipboard.value;
}
```

## 実験内容1: 共有ディレクトリを汚さない

### どういうこと？

共有ディレクトリとは、Assets/Editor, Assets/Plugins/iOSなどを複数のAssetが共通して利用するディレクトリの事を言っています。
[Assets/Editor以下とか、Assets/Plugins/iOS以下にファイルを置くのはやめよう。](http://d.hatena.ne.jp/kyubuns/20140615/1402825919)
Plugins/iOS以下に置けば、何もしなくてもXCodeProjectにコピーされビルドされて楽です。
が、上記エントリで書いたように「どのファイルがどのAssetの物？」という管理が非常に面倒くさくなります。

### 解決案

リポジトリを見ていただいたら分かるように、今回は全てAssets/UniClipboard以下にファイルをまとめています。
https://github.com/kyubuns/UniClipboard/tree/master/Assets

これは、以下のAssetを利用して、PostProcess時にXCodeProject内に必要なコードをコピーしています。
(ちなみに。EveryplayのUnity対応でも使用されていました。)
https://github.com/dcariola/XCodeEditor-for-Unity

単純な話ですが、これをやるだけで利用する側はAssetの移動/削除/更新などが非常にやりやすくなります。

## 実験内容2: NativePluginのコードで、ARCを利用する

### なにそれ？

Obj-Cには、ARC(Automatic Reference Counting)というメモリ管理方法があります。
これを利用することで、コード内にいちいち[hoge autorelease]等を書かなくて良くなります。
参考: [Objective-C ARCによるメモリ管理](http://cx5software.sakura.ne.jp/blog/2011/12/31/objective-c-memory_management_by_arc/)

しかし、AssetStoreに売っているAssetを見ても、あっち見てもこっち見てもautoreleaseだらけです。
手動でメモリ管理とかめちゃくちゃ怖いのですが、これはUnityが出力するObj-Cのコードが、実はARCに対応していないためです。

### 解決策

ググってみると、どうやらARCはファイル単位で有効/無効が設定できるようです。
参考: [ファイル毎に ARC の有効・無効を設定する](http://program.station.ez-net.jp/special/objective-c/llvm/arc/enable-file.asp)

ということで、（実は今回は必要ないのですが）objc_arcフラグが付いていないとビルドが通らないNativePluginを書いてみました。
https://github.com/kyubuns/UniClipboard/blob/master/Assets/UniClipboard/Editor/Plugins/iOS/uniclipboard.mm#L1

そして、先ほど紹介したXCodeEditor-for-Unityを利用して、特定ファイルだけにARC有効フラグをつけています。
https://github.com/kyubuns/UniClipboard/blob/master/Assets/UniClipboard/Editor/XCodePostProcess.cs

## 補足

ここまで大絶賛してきたXCodeEditor-for-Unityですが、1つだけUnityPro使用時にだけ踏み得るバグが存在するのでissueに回避策と併せて投稿してあります。
https://github.com/dcariola/XCodeEditor-for-Unity/issues/10

あと、obj-Cは今日生まれて初めて書いたので、何かあったらご指摘ください。

## 併せて見たいシリーズ

* [Prefab in Prefab](http://qiita.com/kyubuns/items/5741e5281f4bb8de656c)
* [Unity CSV Viewer](http://qiita.com/kyubuns/items/b2dcbf9c58c5b28e0ff4)
