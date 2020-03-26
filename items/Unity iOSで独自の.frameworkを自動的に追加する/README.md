# 前準備

まずは、XCodeEditor-for-Unityをつっこんでください。
http://qiita.com/kyubuns/items/28dd316ef2415fc9a48d

# やりかた

*.projmodsに以下のように記述します。

```lang:hoge.projmods
{
  "group":       "Hoge",
  "libs":        [],
  "frameworks":  ["AdSupport.framework"],
  "headerpaths": [],
  "files":       ["/Users/kyubuns/hoge/fuga/piyo/Dokuzi.framework"],
  "folders":     [],
  "excludes":    ["^.*.meta$", "^.*.mdown^", "^.*.pdf$"],
  "buildSettings": {}
}
```

AdSupport.frameworkはサンプルとして書いてるだけなので気にしないでください。
標準フレームワークは"frameworks"に記述するのですが、独自フレームワークはfilesに書けばOKです。

# 関連記事

[Unity iOSでURL Schemeを利用してアプリを起動できるようにする](http://qiita.com/kyubuns/items/6cbc982a3d96e8f4c5ce)
[Unity iOSで自動的に画像リソースを追加する](http://qiita.com/kyubuns/items/8e5b56e51091aa5a48ba)
