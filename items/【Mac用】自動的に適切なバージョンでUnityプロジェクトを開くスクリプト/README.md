スクリプトはgistにあげてあります。
[open_unity.sh](https://gist.github.com/kyubuns/d305a7fc78cade66fd87)

# はじめに

複数のUnityプロジェクトを触っていると「あ！このプロジェクトはUnity5.1で開かなきゃいけなかったのに間違えてUnity5.2で開いちゃった！」みたいな事案がよく発生します。
また、そもそもバージョン違いのUnityを起動するのがめんどくさい、というような方向けです。
Mac用ですが、Linux版のUnityEditor(beta)でも使えるかも？

# 使い方

このスクリプトを使う前に、/Applications/Unityディレクトリの名前を以下のように変更しておく必要があります。
ex.
/Applications/Unity5.1.4b15
/Applications/Unity5.2.1p4
/Applications/Unity5.2.2p1

後は、
```open_unity ~/unity_projects/hoge```
という風に、第1引数にUnityプロジェクトのパスを渡すだけです。

# やっていること

ProjectSettings/ProjectVersion.txtに書かれているUnityバージョンを探しに行きます。
見つかれば、そのバージョンのUnityで開きます。

見つからなかった場合、そのバージョンに近いUnityを探します。
例えば、ProjectVersion.txtに5.2.1p3が指定されていた場合は、
5.2.1p3 -> 5.2.1p* -> 5.2.1* -> 5.2.* -> 5.*というバージョンを探しに行き、見つかればそのバージョンで開くかどうかをユーザーに確認します。

# Unityバージョンの指定

```
open_unity ~/unity_projects/hoge 5.3
```
という風に、第2引数にバージョンを指定するとそのバージョンで開こうとします。

# ダウンロード

[open_unity.sh](https://gist.github.com/kyubuns/d305a7fc78cade66fd87)
/usr/local/bin/の下とかにいれとくと便利です。

# unienv

unityのインストールにはunienvが便利です。
```sudo unienv install 5.2.1p4```
と叩くだけでUnityが入っちゃいます！便利！
https://github.com/fum1h1ro/unienv
