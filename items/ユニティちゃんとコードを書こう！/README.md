# はじめに

この記事は、[Unity アセット真夏のアドベントカレンダー 2014 Summer！](http://unityassetjp.doorkeeper.jp/events/12843)17日目の記事です。
前半はユニティちゃん好きな方向け、後半はUnityAsset作る方向け　と、詰め込みすぎて2段構成になっちゃいました。

# ユニティちゃんとコードを書こう！

この！ちきしょー！やめてやる！！コードを書くのなんかやめ…て…え！？見…てる？

![unity-chan1.png](https://qiita-image-store.s3.amazonaws.com/0/6459/24046a51-862b-2f8d-1ec8-b971e79398f2.png)

Hierarchyウィンドウのユニティちゃんが僕を見てる？ 
Hierarchyウィンドウのユニティちゃんが僕を見てるぞ！ユニティちゃんが僕を見てるぞ！Hierarchyウィンドウのユニティちゃんが僕を見てるぞ！！ 

![Unity-chan2.png](https://qiita-image-store.s3.amazonaws.com/0/6459/27b5a77e-94ff-cc96-b581-9cb6d861cb27.png)

実行中はユニティちゃんちゃんが走ってるぞ！！！よかった…UnityEditorは、まだまだ捨てたモンじゃないんだねっ！
いやっほぉおおおおおおお！！！僕にはユニティちゃんちゃんがいる！！やったよUnity！！ひとりでゲーム作れるもん！！！

# ということで

Hierarchyウィンドウからユニティちゃんが見守っていてくれるアセットを作りました。
通常時は立ちモーション、実行中は走ります。
また、実行中に一時停止すると走ったまま止まったりと微妙な工夫が施されています。
が、それだけです。

AssetStore(まだ)
[github](https://github.com/kyubuns/mini_unity_chan_on_unity)

AssetStoreに出して「Assetの紹介！」という体で紹介しようとしたのですが、
Rejectくらったので、まだAssetStoreにでてません！
また出し直します！すみません！

# Assetを見て学ぼう！

ここから後半戦です。
今回のアセットのコードと共に、このユニティちゃんのアセット作るときに使ったもろもろを紹介します。

## Assets/Editor直下には何もおかない

[今回のディレクトリ構成](https://github.com/kyubuns/mini_unity_chan_on_unity/tree/master/Assets/MiniUnityChan)を見て頂くと分かるのですが、
Assets/Editor
ではなく、
Assets/MiniUnityChan/Editor
以下にコードが入るようにしています。
「Editorや、Resourcesディレクトリ以下にファイルを置け」という時は、別にAssets直下じゃなくても問題無いです。
なので、Assets/Editor直下にファイルを入れて「あれ！？このファイル何のAssetのやつだっけ・・・」とユーザーさんが困らないようにしましょう。

## namespaceを分ける

[MiniUnityChan.Core](https://github.com/kyubuns/mini_unity_chan_on_unity/blob/master/Assets/MiniUnityChan/Editor/Core.cs)を見て頂くと分かりますが、ちゃんと

```lang:View.cs
namespace MiniUnityChan
{
```
namespaceを分けています。
Importされたプロジェクトに、既に同じ名前のクラスが存在する！とか、
他のAssetと名前が衝突する！ということを避けるためです。
Assetとして配布するときはやっておきましょう！

## Assetがどこに移動されても大丈夫なようにしておく

ImportされたAssetは、使う人によってどこのディレクトリに移動されるか分かりません。
（自分も、AssetStoreからImportしてきたAssetは、特定の1ディレクトリ以下にまとめるようにしています。）
どこに移動されても問題無いように作っておくのが吉です。

[MiniUnityChan.View.LoadSprite](https://github.com/kyubuns/mini_unity_chan_on_unity/blob/master/Assets/MiniUnityChan/Editor/View.cs#L54)

```lang:View.cs
var spritePath = new Uri(Directory.GetFiles(Application.dataPath, spriteName, SearchOption.AllDirectories)[0]);
var projectRootPath = new Uri(Application.dataPath);
var relativeUri = projectRootPath.MakeRelativeUri(spritePath);
var loadSprite = AssetDatabase.LoadAllAssetRepresentationsAtPath(relativeUri.ToString());
```

ここのコードはちょっと面倒なことをしています。
1つのファイルを読んできたいだけなのですが、
Directory.GetFilesで、特定のファイル名を[Application.dataPath](http://docs.unity3d.com/ScriptReference/Application-dataPath.html)以下から探してきます。
しかし、これによって帰ってくるのはフルパスなので、projectRootPathからの相対パスを作ってやって、やっと[AssetDatabase.LoadAllAssetRepresentationsAtPath](http://docs.unity3d.com/ScriptReference/AssetDatabase.LoadAllAssetRepresentationsAtPath.html)に渡すことができます。
他に楽な方法があれば教えてください・・・

## 無理矢理Windowを再描画する

普通はこんなことしないと思うのですが・・・
各種EditorWindowは、基本的に何かのイベントがあったとき（クリックされた時とか）、必要にならないとWindowの再描画は走りません。
今回ユニティちゃんをアニメーションさせたかったので、HierarchyWindowを無理矢理再描画しています。
[EditorApplication.RepaintHierarchyWindow](http://docs.unity3d.com/412/Documentation/ScriptReference/EditorApplication.RepaintHierarchyWindow.html)

## 他のAssetも見て学ぼう！

ご存じの通り、[AssetStore](https://www.assetstore.unity3d.com/)にはたくさんのAssetがあります！
「こういう機能を作りたいんだよなー、でもこのAssetはちょっと違うかも・・・」という時、似ているAssetを探してきてソースコードを読みあさると勉強になるのでオススメです！
コードがdll化されていて読めない物もあるので、有料Assetは購入前にPackageContentsを確認するようにしましょう！

![Asset_Store_-_CSV_Viewer.png](https://qiita-image-store.s3.amazonaws.com/0/6459/5d63eac6-3b10-5bd4-781f-124cf5b99f25.png)

# おしまい

アドベントカレンダー8/18の担当は [enpel](http://twitter.com/enpel) さんです、よろしくお願いします！

# 宣伝

## CSV Viewer

![9459fabc-fffc-4c73-6a81-7fda866291a4.png](https://qiita-image-store.s3.amazonaws.com/0/6459/a22888ea-729d-aa80-06f3-018fa23ccd06.png)

[宣伝記事](http://qiita.com/kyubuns/items/b2dcbf9c58c5b28e0ff4)
[AssetStore](https://www.assetstore.unity3d.com/en/#!/content/18424)

AssetStoreでCSV ViewerのAssetだしてます。
10万行ぐらいあるCSVとかもサクサク動きます。
GUIは基本的に描画がめちゃくちゃ重いので、画面に描画されない範囲は描画しない、とか色々頑張ってるAssetです。

# 関連記事

せっかくなので、Unity関連で、自分が今までに書いた役に立ちそうな記事へのリンクを張っておきます。

## Asset配布する人向け
* [Assets/Editor以下とか、Assets/Plugins/iOS以下にファイルを置くのはやめよう。](http://d.hatena.ne.jp/kyubuns/20140615/1402825919)
* [ArgumentException: Getting control n's position in a group with only n controls when doing Repaint Aborting](http://d.hatena.ne.jp/kyubuns/20140601/1401597362)
* [Unity EditorWindowでUndoしたいとき](http://d.hatena.ne.jp/kyubuns/20140527/1401190984)
 
## iOS系

* [Unity iOSでURL Schemeを利用してアプリを起動できるようにする](http://qiita.com/kyubuns/items/6cbc982a3d96e8f4c5ce)
* [Unity iOSでURL Schemeを使って情報を受け取る](http://qiita.com/kyubuns/items/c429c0f6cbe68389f7e8)
* [Unity iOS XCodeに自動で依存ライブラリの追加する](http://qiita.com/kyubuns/items/28dd316ef2415fc9a48d)
* [Unity iOS NativePluginのコードで、ARCを利用する](http://qiita.com/kyubuns/items/24d33044e77b9fa614b3#2-6)
