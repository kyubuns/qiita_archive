### まえがき

Unityでクライアントをビルドして、ビルドレポート見てみると
「このテクスチャはAssetBundleになるはずだから、ビルドには乗らないはずなのに・・・」っていうのが含まれてたりしますよね。
そんなとき、どこから参照されてるか調べるツールは便利！
でも遅い！！！1個調べるのに数分かかる！！！


### で、

普段はmetaファイルをテキストで開いて、guidをgit grepかけてたんですが
いい加減GUIでやりたいなってことで、[ina-amagamiさんが作ってたunity-reference-viewer](https://github.com/ina-amagami/unity-reference-viewer)をforkしてgit grep対応してみました。
[kyubuns/unity-reference-viewer](https://github.com/kyubuns/unity-reference-viewer)

そう、検索を早くする工夫なんてこの記事には何も出てこないんです。
ただgrepをgit grepに変えただけなんです。
当たり前ですが、プロジェクト丸ごとgit管理してないと使えません。

使い方はina-amagamiさんがめっちゃ丁寧に書いてくれているので[こちらをご覧ください。](https://amagamina.jp/reference-viewer/)

### 計測結果

|コマンド|時間|
|:------|:------|
|grep|28.17s|
|git grep|0.53s|

なんと驚異の53倍速。
