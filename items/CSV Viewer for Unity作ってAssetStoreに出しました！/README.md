![csv_viewer.png](https://qiita-image-store.s3.amazonaws.com/0/6459/9459fabc-fffc-4c73-6a81-7fda866291a4.png)

[Asset Store - CSV Viewer](https://www.assetstore.unity3d.com/en/#!/content/18424)

# はじめに

ゲームの設定ファイルにCSVを使ったことはありませんか。
Unity4.3からは、TextAssetとして.csvを認識するようになりました。
が、CSVを見るためだけに、いちいちExcel等を立ち上げるのはめんどくさい！
という方向けのAssetです。

（AssetStoreに物を出してみたかった！という超個人的な話もあります。）

# 特徴1

「なるべく軽く、早く」を考えて作っていて、検証に使っているのは1万行あるcsvです。
巨大csvでも大丈夫、ダブルクオート入りのcsv等ももちろんOKです。(RFC4180準拠)

![Untitled_-_wafer_-_PC__Mac___Linux_Standalone.png](https://qiita-image-store.s3.amazonaws.com/0/6459/0eebc418-df79-6bd1-2c5b-896ee94515ad.png)

# 特徴2

高速な検索が可能です。
また、{column_name}=={value}, {column_name}>{value}のような検索も可能です。

![Untitled_-_wafer_-_PC__Mac___Linux_Standalone_search.png](https://qiita-image-store.s3.amazonaws.com/0/6459/7208fdd4-001f-a329-2a51-65a030dce7fe.png)

# 出来ないこと！

* Viewerです。編集は出来ません。
* セルの中に改行を含んでいるCSVの表示はサポートしていません。(パース自体は正常に行われますが、1行目しか表示されません。)

# 購入はこちらから！

[Asset Store - CSV Viewer](https://www.assetstore.unity3d.com/en/#!/content/18424)

# サポート

バグ報告、ご意見、ご感想、お待ちしております。
mail: kyubuns@gmail.com
twitter: @kyubuns

# オススメ記事情報

[Unity Prefab in Prefab作りました！](http://qiita.com/kyubuns/items/5741e5281f4bb8de656c)
こっちはタダです。
