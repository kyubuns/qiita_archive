# まえおき

Photoshopには、全てのレイヤーを個々のファイルに書き出すための
「レイヤーをファイルに書き出し(Export Layers to Files)」というスクリプトが標準で入っているのですが、
これがめちゃくちゃ遅いため、書きなおして速くしてみました。

レイヤー25枚のpsdで使うと55秒かかっていたのが、
このスクリプトに置き換えると、3秒で終わります。

# 準備

* 以下のURLから、ExportLayersToFile.jsxをダウンロード
https://raw.githubusercontent.com/kyubuns/yogurt/master/ExportLayersToFile.jsx

* ダウンロードしたファイルを'/Applications/Adobe Photoshop CC 2016/Presets/Scripts/'以下にいれる
(Windowsは、C:\Program Files\Adobe\Adobe Photoshop CC (64 Bit)\Presets\Scriptsなど。)

* Photoshopを起動していたら再起動

# 使い方

* 対象のpsdを開く

* ファイル->Scripts->ExportLayersToFile をクリック

# あとがき

必要だった最低限の機能しか実装してないので、これも欲しい！みたいなものがあればコメントでお願いします。
