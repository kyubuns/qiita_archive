# 追記(2015.10.31)

Unity5ではそもそもチェックマークいれるだけでiOSプロジェクトに含まれるようになったので
この記事は不要です・・・

-----

# 追記(2015.03.23)

Unity5ではXcode Manipulation APIというのがあるらしいです。
https://gist.github.com/keijiro/975b8439d25d58bf62ce

-----

XCodeEditor-for-Unityの紹介記事です。

## なにができるの

XCodeEditor-for-Unityを使うと、BuildPostProcessで自動的に依存ライブラリを追加したり、なんやかんやと出来ます。
便利。
https://github.com/dcariola/XCodeEditor-for-Unity/blob/master/Readme.mdown

## コード

本家の方は1年ぐらい更新されていなくてUnity4.5でビルドが通らないので、forkして修正版上げてます。

* 本家
https://github.com/dcariola/XCodeEditor-for-Unity

* Unity4.5でビルド通るようにしたバージョン
https://github.com/kyubuns/XCodeEditor-for-Unity

* 使ってみたプロジェクト
https://github.com/kyubuns/UniClipboard/tree/master/Assets/UniClipboard/Editor/

## 入れ方

READMEよんでください。
[自分のやつ](https://github.com/kyubuns/XCodeEditor-for-Unity) はREADMEが最新の状態になってます。

## ちなみに

FacebookSDK for Unityつっこむと衝突するので片方消してください。

## 関連記事

* [Unity iOSでURL Schemeを利用してアプリを起動できるようにする](http://qiita.com/kyubuns/items/6cbc982a3d96e8f4c5ce)
