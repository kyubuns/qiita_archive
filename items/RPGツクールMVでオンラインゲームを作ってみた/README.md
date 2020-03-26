<img width="740" alt="MMOもどき_v1_0_1.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/0091c244-4092-4031-a150-6d09fe6989a1.png">
名前もねぇ！チャットもねぇ！鯖もそれほど動いちゃねぇ！
他の人と位置同期しているだけ　のゲームです。
ゲームなのか・・・？

百聞は一見にしかず というので、まあまずは遊んでみてください。
http://kyubuns.net/MMOM
iPhoneでも動きます！たぶんAndroidでも動きます！
人いないときは2窓してみてください。

# RPGツクールMVって？

RPGツクールの最新作。
今回はWindowsの他に、Mac, iOS, Androidそしてhtml5に出力できちゃいます。
https://tkool.jp/mv/

英語版が先週発売、日本語版は12月発売。
http://store.steampowered.com/app/363890/

# やりたかったこと

RPGツクールを使ってオンラインゲームが作れるか という実験プロジェクトです。
ネットワーク部分はsocket.ioに丸投げしたので深い説明はしません。
ツクールとのつなぎ込み部分をメインに書いていきます。

# やったこと

* プレイヤーの位置同期
* ルーム機能（マップを移動すると、そのマップ内のユーザーの座標のみ同期する）

# 実現方法

## サーバー

サーバー側は特に目新しいことはやっていません。
socket.ioのroom機能を使って、クライアントから飛んできた移動後座標を他のクライアントにブロードキャストしてるだけです。
詳細は[socket.ioのDocs](http://socket.io/docs/)を見てください。
https://github.com/kyubuns/MMOM/blob/gh-pages/Server/server.coffee

## クライアント

https://github.com/kyubuns/MMOM/blob/gh-pages/Dev/ClientPlugin/Network.coffee
クライアントも普通にsocket.io使ってるだけです。

・・・だと、説明にならないので、詰まった点を紹介していきます。

### クライアントでsocket.ioをどうやってincludeするか

~~jsからjsをincludeするかなぁ・・・とか色々考えたんですけど、
よく考えたらindex.htmlが勝手に触れる場所にあるので、素直に```<script type="text/javascript" src="js/libs/socket.io.js"></script>```って書きました。
https://github.com/kyubuns/MMOM/blob/gh-pages/Client/index.html#L18~~

追記:

socket.io.jsのままjs/plugins以下に配置すると正常に読み込めなかったので上記の方法をとりましたが、
socket_io.jsなどにリネームしてから置いたら正常に動作しました。

### どうやってイベントを増やすか

プレイヤー1人＝1マップイベント。
つまり、誰かが接続してきたらUnityのPrefabのようにイベントを増やしてやらないといけない。
ということで、海外のフォーラムあさってたらイベントをクローンするプラグインがありました。
http://forums.rpgmakerweb.com/index.php?/topic/46527-orange-custom-events/

### ツクール -> プラグインにどうやって情報を渡すか

プラグイン側に全ての情報があるので、ここはそれほど困らなかった。
例えば、プレイヤーの座標なら```$gamePlayer.x, $gamePlayer.y```に入っている。とかを頑張って読み解く。

### プラグイン -> ツクールにどうやって情報を渡すか

問題はこちら。
プレイヤーAの位置がサーバーから飛んできたとして、どうやってツクール上のイベントの位置に反映するか。
また、このあたりまで作って「スクリプトばっかり書いてるとツクールらしさがないな・・・」ということで、イベントを無理にでも使ってみます。

<img width="1038" alt="ID_001_-_Event_Editor_and_MMOもどき_v1_0_1_-_RPG_Maker_MV.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/f5ae467f-7740-3e40-f740-fc81ce0b4265.png">

1行目のScriptは、このメソッドを呼んでいます。
https://github.com/kyubuns/MMOM/blob/gh-pages/Dev/ClientPlugin/Network.coffee#L47

```lang:Network.coffee
$gameVariables.setValue(10, 1)
$gameVariables.setValue(11, info['x'])
$gameVariables.setValue(12, info['y'])
```

ツクール変数の10, 11, 12に、それぞれ1(生きてるか死んでるか)、x座標、y座標を書いてます。
それ以下は、ツクールの変数を見て移動するイベントです。

### バグ: タイトル画面に戻るとエラーが発生する

メニューからタイトル画面に戻ると、socketは生きたままマップ情報が破棄されてしまうので、
サーバーからデータが飛んできたタイミングでエラーになってしまっていた。
タイトル画面に戻ることのある遷移は、メニュー以外にも戦闘敗北時などが考えられるので、タイトル画面側で対処することにした。
タイトル画面が表示されるときにsocketが生きていれば殺す。
https://github.com/kyubuns/MMOM/blob/gh-pages/Dev/ClientPlugin/Network.coffee#L81

# 反省点

* RPGツクールでやる意味あったのか・・・？というぐらいRPGツクールの機能が使えていない。
* 戦闘の同期とかまで作ったら面白かったのかも。
* プラグイン->ツクールに値を渡す方法がスマートじゃない・・・
* 他プレイヤーの指定された位置まで移動するアルゴリズムが頭良くないため、稀にひっかかる
* この記事をテンション高めに書き上げたので色々書き漏らしてる気がする。

# あとがき

ということで、今回のツクールはhtml5で出来ることは何でもできちゃいますね。
面白いゲームが出てくるのを楽しみにしています！
アプリ内課金とか実装できるのかなこれ・・・

質問などは、コメントか[twitter](http://twitter.com/kyubuns)までお気軽にどうぞ。

# ちなみに

あの敵、全く調整せずに置いたんですけど勝てるらしい

![S__42885148.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/df780157-4adf-3e62-9dcd-35137bd27323.jpeg)
