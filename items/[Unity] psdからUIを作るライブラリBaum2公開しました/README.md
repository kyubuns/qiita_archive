psdからUIを作るライブラリ、"[Baum2](https://github.com/kyubuns/Baum2)"を公開したのでご紹介します。

# なにこれ？

こういうpsdから、

![Monosnap 2017-04-16 14-50-33.png](https://qiita-image-store.s3.amazonaws.com/0/6459/f222cd7d-42c9-1949-3dbe-bb004cd73374.png)

このようなUIが作れます。

![Monosnap 2017-04-16 14-51-49.png](https://qiita-image-store.s3.amazonaws.com/0/6459/bb6535ae-8d61-d299-2236-622c07bc8c01.png)

レイヤーだけでなく、グループ(Photoshop上のレイヤーをまとめるやつ)も再現されている上に、
*Buttonと書いたグループは自動的にボタンになりますし、
テキストレイヤーはUnityEngine.UI.Textになります。

## どうするの？

psdからPhotoshopScriptで中間ファイルを生成し、
その中間ファイルをUnity上にドラッグ＆ドロップするだけで、
prefabを自動的に生成してくれます。

Photoshop上ではButtonにしたければグループ名の最後にButtonとつける等、ちょっとしたルールがあります。

コード的には

```csharp
ui.Get<Text>("Welcome/Text")
```

この1行で、Welcomeグループの下にあるTextというレイヤーが取れます。

![Monosnap 2017-04-16 01-40-03.png](https://qiita-image-store.s3.amazonaws.com/0/6459/7dbf07fa-7012-cc2a-bbff-186e32149856.png)

後はUnityEngine.UI.Textが返ってくるので、煮るなり焼くなり好きにしてください。

サンプルコードはこんな感じ。

```csharp
using UnityEngine;
using UnityEngine.UI;
using Baum2;

public class Sample : MonoBehaviour
{
	[SerializeField]
	private GameObject UIPrefab; // Baum2が生成したprefabを指定する

	public void Start()
	{
		var ui = BaumUI.Instantiate(gameObject, UIPrefab);

		ui.Get<Text>("Welcome/Text").text = "Welcome to Fuga!";
		ui.Get<Button>("HogeButton").onClick.AddListener(() =>
		{
			UI.Get<Text>("Welcome/Text").text = "Welcome to Hoge!";
		});
	}
}
```

# 方針

世の中には数多くのpsd->UI変換Assetがありますが、Baum2の方針はこうです。
Baum2が生成したprefabは手動でいじってはいけません。
Componentをつけたり、参照を持ってきたりしたくなるのですが、そこは、その他の利便性のために割り切りました。
「baum2が生成したprefabは、スクリプトから開く」「Prefab内部のButtonなどにはスクリプトからアクセスする」必要があります。
そのおかげで、psdを更新するとprefabが自動で作り直された上に参照も切れず、psd内のグループ構成変更に強いなどの特徴があります。

# 特徴

## psd内のグループ構成が変わってもコードに極力影響が出ない設計

```ui.Get<Button>("HogeButton")``` というメソッドは、psd上でHogeButtonという名前がついているグループを取得してきてくれます。
ポイントは、この引数がフルパスで無くても構わないという点です。
psd上でのHogeButtonは、"ButtonSample"グループの中にあるので、```ui.Get<Button>("ButtonSample/HogeButton")```と書いても動きます。
AGroupの中のBGroupの中のCGroupの中にあるHogeButtonは、```ui.Get<Button>("AGroup/BGroup/CGroup/HogeButton")```でもいいし、```ui.Get<Button>("CGroup/HogeButton")```でも良いです。
これによって、デザイナーさんがpsd上のグループ構成を変更しても、コードに影響が出にくくなっています。
ちなみに、HogeButtonが2つある場合```ui.Get<Button>("HogeButton")```はエラーになります。
```ui.Get<Button>("AGroup/HogeButton")```のように、直前のグループ名を付ければオッケーです。

## Resourcesディレクトリを使わない

Resourcesディレクトリを使うと起動時間が遅くなるとかなんだとかかんだとか、Resourcesディレクトリは極力使うなって話があります。
Baum2は、全てprefabからの参照で握っているためResourcesを使っていません。
よって、どこのシーンにも配置されていないUIはビルドには含まれません。

## 自動的に9sliceがかかる

[OnionRing](http://qiita.com/kyubuns/items/4327bb38ff22989ba7ed)というライブラリを使って、各Spriteは自動的に9sliceがかかります。

before: ![Sample_ButtonSample_FugaButton_Background.png](https://qiita-image-store.s3.amazonaws.com/0/6459/842daae1-2865-7253-be42-afc392cc23f9.png)
after: ![Sample_ButtonSample_FugaButton_Background.png](https://qiita-image-store.s3.amazonaws.com/0/6459/a15e7b86-025b-c58b-a7b0-bf87dde275fb.png)

## 1つのUIのSpriteが自動的にまとめられる

9sliceがかかった上に、
UnityのSpritePack機能によって、1つのUIに使われている画像は自動的に1つのAtlasにまとめられます。

![Monosnap 2017-04-16 01-57-05.png](https://qiita-image-store.s3.amazonaws.com/0/6459/b6dc961e-fb74-d7c4-eccc-fa42d7759377.png)

## 実行時の速度もそこそこ早い

UIの生成自体は、ただのprefabのinstantiateなのでこれ以上どうしようもないぐらいの速度が出てます。
コードに関して、「Getを呼ぶたびに検索してたら遅そう」という印象を受けるかもしれませんが、
prefabを生成する時に名前からGameObjectをひけるキャッシュも生成しているので、ここも問題になることは無いでしょう。

![Monosnap 2017-04-16 02-04-55.png](https://qiita-image-store.s3.amazonaws.com/0/6459/1e2f081c-a669-f785-b7fb-357d35fa5625.png)

## Photoshop上でEffectsとかも使える

![Monosnap 2017-04-16 01-55-06.png](https://qiita-image-store.s3.amazonaws.com/0/6459/91983087-6e9f-90c9-b914-17817ab15464.png)

こういうの、ちゃんと反映されます。
ちなみにレイヤー名の先頭に#をいれると出力されないレイヤーを作ることが出来ます。

## 簡単なPivotはpsdから指定可能

![Monosnap 2017-04-16 01-55-06.png](https://qiita-image-store.s3.amazonaws.com/0/6459/91983087-6e9f-90c9-b914-17817ab15464.png)

ルート直下のグループ名の後ろに、```@Pivot=TopRight```と書いておくと

![Monosnap 2017-04-16 02-07-49.png](https://qiita-image-store.s3.amazonaws.com/0/6459/b48958bf-2445-36ce-ed67-398e288a7389.png)

このように右上にくっついてきてくれます。

## Textも頑張って再現してる

psd上で指定されているFontと同じフォントがUnity上でインポートされていれば、
結構頑張ってTextの位置、サイズ、色なども再現しています。
しかし、PhotoshopとUnityで文字列描画の方法が全然違うため完全には再現しきれていません。
特に書くことはないのですが、頑張ったところなのでアピールしたかっただけです。

# あとがき

リポジトリは[こちら](https://github.com/kyubuns/Baum2)
「ここどういう仕組みになってるの？」「こういう時どうするの？」などなどは[twitter](https://twitter.com/kyubuns)か、この記事のコメント欄までお気軽にどうぞ。
バグ報告は[twitter](https://twitter.com/kyubuns)か[githubのissue](https://github.com/kyubuns/Baum2/issues)にどうぞ。
