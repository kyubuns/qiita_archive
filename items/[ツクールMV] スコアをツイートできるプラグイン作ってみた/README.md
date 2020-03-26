2015/12/17発売！ [RPGツクールMV](https://tkool.jp/mv/)用のプラグインです。
ブラウザ上で遊べるミニゲームによくある「スコアをツイートする」機能が簡単に作れるようになります。

# リンク集

とりあえず試してみたい方: [サンプルプロジェクト](http://kyubuns.net/rpg_maker/TweetPlugin)
プラグイン: [github: kyubuns/rmmv_tweet_plugin](http://raw.githubusercontent.com/kyubuns/rmmv_tweet_plugin/master/Tweet.js)

# で、何ができるの？

PluginCommandを使って、好きな文言をツイートしてもらうことが可能です。
メッセージダイアログと同様に、変数展開もできるのでツイート文言中にスコアを入れることが可能です。
<img width="1033" alt="Plugin_Command_and_ID_001_-_Event_Editor_and_Minimum_-_RPG_Maker_MV.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/7a90b35b-03a5-fece-0414-50c1854a4f27.png">

# 使い方

PluginCommandで「Tweet hogefuga」を実行すると、hogefugaの部分がツイートされます。
変数や主人公名の展開は、通常のメッセージダイアログと同じ記法で行えます。

プラグイン読み込み時に、ツイートの後ろにつけるURLやviaも設定することが出来ます。
<img width="474" alt="Plugin_and_Plugin_Manager.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/d7a55b67-0328-ef4a-6d17-e50a483292bd.png">

# 注意点

このプラグインが動作しないブラウザがあります。
以下に挙げたブラウザ以外は動作未確認です。動作報告お待ちしております。
（対応プルリクもお待ちしております。）

## 動作確認済み環境

以下のブラウザでは動作確認が行われています。

* Mac
 * Chrome(Version47)
* iOS
 * Safari(iOS9) (Twitter公式アプリのインストールが必要)
 * Chrome(Version47)

## 動作しないことが確認されている環境

以下のブラウザでは動作しないことが確認されています。
この環境でツイートしようとした場合には、プラグインが対応していない旨が表示されます。

* Mac
 * Safari(Version9)
* iOS
 * Firefox(Version1.2)


# あとがき

iOS対応が辛かった。

意見・感想・質問などなどは、[Twitter @kyubuns](http://twitter.com/kyubuns/) または ここのコメント欄 まで お気軽にどうぞ。
プルリクもお待ちしてます。

この記事は、[RPGツクールMVアドベントカレンダー2015](http://qiita.com/advent-calendar/2015/rmmv) 19日目の記事の一部です。

# リンク集

とりあえず試してみたい方: [サンプルプロジェクト](http://kyubuns.net/rpg_maker/TweetPlugin)
プラグイン: [github: kyubuns/rmmv_tweet_plugin](http://raw.githubusercontent.com/kyubuns/rmmv_tweet_plugin/master/Tweet.js)
リポジトリ: [github: kyubuns/rmmv_tweet_plugin](http://github.com/kyubuns/rmmv_tweet_plugin/)
