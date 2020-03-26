# はじめに

- 開発版を社内/身内にテストしてもらっている時に使える手法です。
- 本番環境で使いたい場合は、もっとまともなものを入れましょう。

# 実機でもログが見たい！

実機で動作中のゲーム、「○○のボタン押したらゲームが止まったんだけど」って言われた時どうしますか？
パターンごとに、サクッとログを取れる手段をまとめました。
USBで繋いでadb繋いで・・・とか、XCodeに繋いで・・・とかやる時代は終わりました。嘘です盛りました、たまに使います。

## Level1 : 借りて良い端末 & 開発PCと同じwifiに繋がっている場合

社内端末など、自分が借りて操作して良い端末かつ、同じwifiに繋がっているDEVELOPMENT_BUILDの時(正しくはAutoconnect Profilerが有効になっている時)に使える技です。
<img width="561" alt="Monosnap 2018-12-30 22-33-49.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/3f3e7fd6-cd61-7c93-37a1-93d92b9e2e86.png">
ConsoleWindowから同じwifiに繋がっている実機のログを見れます。
めちゃくちゃ便利。

## Level2 : 借りて良い端末の場合

[SRDebugger](https://assetstore.unity.com/packages/tools/gui/srdebugger-console-tools-on-device-27688)ってAssetがめちゃくちゃ便利で、実機上でコンソールログが見れます。
画面の左上をトリプルタップ、という特殊操作をしないと開かないのでデバッグビルド感が出にくいのもオススメポイントです。
類似Assetをいろいろ試しましたが、コイツの安定感とデバッグ用のボタンを簡単に作れる機能に惹かれて愛用してます。
SafeArea対応されてないのが玉に瑕。

## Level3 : 借りれない端末 or 相手が遠くにいる場合

これよくあるんですよね、家で試してたらエラーでたとか。
相手がエンジニアならまだしも、そうでない場合はエラーログをどうにかして送って、というのもなかなか酷です。
そんなときに便利なAssetがこちらの[UniMail](https://github.com/kyubuns/UniMail)。
UniMailって名前なのですが、よく見るとUniBugReporterってパッケージも一緒に配布されています。
これを使うことで「エラーログが出た時に自動的にログ情報が入ったメーラーを立ち上げる」ということが出来ます。
API叩いて自動的にslackに流す、とかなんでも出来ます。

<img width="375" alt="68747470733a2f2f71696974612d696d6167652d73746f72652e73332e616d617a6f6e6177732e636f6d2f302f363435392f61653032326536342d343937662d666662312d636462342d3833646566306339613033622e706e67.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/2704804b-73bc-b3bd-d588-7dd5e9d0d3e9.png">

# UniBugReporter + UniMailの使い方

https://github.com/kyubuns/UniMail/releases
ここからパッケージを両方インストールして、UniBugReporter.csをどこかのGameObjectに貼り付けておいてください。

エラーが出た時にメーラーを立ち上げるサンプルが入っています。
これを参考に独自のIReporterを実装してください。
https://github.com/kyubuns/UniMail/blob/master/Assets/Plugins/UniBugReporter/MailReporter.cs

仕上げに、UniBugReporterの中でReporterを初期化してる箇所があるのでそこを自作のReporterに差し替えれば準備完了です。
https://github.com/kyubuns/UniMail/blob/master/Assets/Plugins/UniBugReporter/UniBugReporter.cs#L16
