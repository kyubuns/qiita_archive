# 追記

新バージョンできました。
http://qiita.com/kyubuns/items/cb01f926966b51a5501c

# なにこれ？

uGUIの9sliced sprite作る時に、
まずPhotoshopで9sliced sprite用の画像を作って、
Unityにインポートした後に、SpriteEditor開いて、1個づつBorderの値入れて・・・めんどくさい！！！という話を聞いたので作ってみました。
どんなツールか知りたい方は、準備は読み飛ばして、使い方のところを見てください。
名前はodenです。

# リポジトリ

https://github.com/kyubuns/oden

# 準備

## Photoshop

https://raw.githubusercontent.com/kyubuns/oden/master/oden.js
このjsファイルをダウンロードして、↓のOS/Photoshopバージョンに対応した箇所に入れてください。
Photoshopを起動している場合は、再起動しないと読み込まれないみたいです。
Mac: /Applications/Adobe Photoshop CC 2014/Presets/Scripts/
Windows: C:\Program Files\Adobe\Adobe Photoshop CC (64 Bit)\Presets\Scripts\

## Unity

https://github.com/kyubuns/oden/blob/master/OdenImporter.cs
このC#ファイルをダウンロードして、UnityプロジェクトのどこかのEditorディレクトリ以下に入れてください。

# 使い方

以下で使用しているpsdファイルは、リポジトリのsample以下に入っています。

## Photoshop

まずは普通に素材を作ります。
この時、Rootにある1つ1つのレイヤーフォルダが、1つのSpriteになるようにします。
また、出力するレイヤーフォルダは、名前の先頭を#にしてください。
(ex. #Button, #Window, ...)
![a1.png](https://qiita-image-store.s3.amazonaws.com/0/6459/c18ef76b-0ffd-7ed1-9706-af22b10ba473.png)

ここに、必要のない箇所を四角形で指定した#Minというレイヤーを追加します。
出力されるpngにこのレイヤーは含まれません。色はなんでもいいです。非表示にしておいても構いません。
![a2.png](https://qiita-image-store.s3.amazonaws.com/0/6459/6f55e5dd-eee8-991d-720a-2af8d784ed48.png)

File->Plugins->Odenを選択すると、psdがあるのと同じディレクトリ内に、png画像及びodenファイルが生成されます。
![b1.png](https://qiita-image-store.s3.amazonaws.com/0/6459/74e3cbe1-5832-563d-87aa-a9b9cce4483f.png)

ちゃんとsliceされてる。

![TestButton.png](https://qiita-image-store.s3.amazonaws.com/0/6459/4f201bf8-5531-6430-58eb-6350633a984c.png)

## Unity

OdenImporter.csを追加したUnityProjectのどこかに、上記で生成したpng, odenファイルを追加します。
この時、pngファイルと対応したodenファイルは同じディレクトリ内に存在しないと認識されません。
![Test_unity_-_unity_-_PC__Mac___Linux_Standalone.png](https://qiita-image-store.s3.amazonaws.com/0/6459/3739ccc0-b5bd-df54-84d1-d1d545a5bc0d.png)

これで作業は終了です！
今追加されたSpriteの設定を見ると、自動的にBorderが指定されていることがわかります。
![Test_unity_-_unity_-_PC__Mac___Linux_Standalone 2.png](https://qiita-image-store.s3.amazonaws.com/0/6459/53940c9a-0030-f371-3435-60974de3157a.png)

![t.png](https://qiita-image-store.s3.amazonaws.com/0/6459/8e1c6744-a295-e02e-efa5-c9120934eeff.png)

# めも

Photoshopさわり始めたてなので用語とか間違ってたらツッコミください。
エラー検出も甘いのでコケたところあったら教えて下さい。
何かあったら、[twitter @kyubuns](http://twitter.com/kyubuns)か[githubのissue](https://github.com/kyubuns/oden/issues)にお願いします。
