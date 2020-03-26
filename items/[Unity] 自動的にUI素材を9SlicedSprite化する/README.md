色んなサイズのボタンが作れる汎用素材を作りたい！とか、
出来るだけUI素材のサイズを小さくしたい！とか言う時に役立つ9SlicedSprite。

でも、9Slice用の画像を作ったり、Unity上でボーダーを設定したりするのがめんどくさい！
ということで、自動化しちゃいました。
名前は **OnionRing** です。
([自動で9 sliced sprite(9 patch)画像を生成してくれるモジュール作った(ruby)](http://qiita.com/kyubuns/items/cb01f926966b51a5501c)のUnity移植版です。)

# リポジトリ

[github: kyubuns/OnionRingUnity](http://github.com/kyubuns/OnionRingUnity)

# で、何が出来るの？

こういう画像を突っ込むと
![before.png](https://qiita-image-store.s3.amazonaws.com/0/6459/ba6211dd-8ece-7866-7e9e-1f3f561cd175.png)

こういう画像が出てきます。
![after.png](https://qiita-image-store.s3.amazonaws.com/0/6459/3f711404-ee29-52a4-0923-d4f17d494db1.png)

uGUI用のSlice設定まで自動で行うことも出来ます。
<img width="300" alt="Sample_unity_-_OnionRingUnity_-_Web_Player__OpenGL_4_1_.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/4c30a682-b636-bd88-b941-8d4a46981625.png">

# 使い方

基本的に、AssetPostprocessor内で使われることを想定しています。
使えるメソッドは1つだけ、
```OnionRing.TextureSlicer.Slice(Texture2D texture)```
これだけです。
すると、SliceされたTextureとBorderの値を持ったやつが帰ってきます。

## サンプル

(以下のスクリプトは、リポジトリ内にすぐに試せるように入っています。)

```OnionRingSampleImporter.cs
using System.Collections.Generic;
using System.IO;
using UnityEngine;
using UnityEditor;

/*
OnionRingSample/Editor/Inディレクトリにpngファイルを入れると、
OnionRingSample/Editor/Outディレクトリにsliceされたpngファイルが出力されるサンプルです。
*/

namespace OnionRing
{
	public sealed class Importer : AssetPostprocessor
	{
		private static Dictionary<string, SlicedTexture> textures = new Dictionary<string, SlicedTexture>();
		
		public void OnPostprocessTexture(Texture2D texture)
		{
			if(!assetPath.Contains("OnionRingSample/Editor/In")) return;
			
			// sliceして、pngで保存
			var slicedTexture = TextureSlicer.Slice(texture);
			File.WriteAllBytes(assetPath.Replace("/In/", "/Out/"), slicedTexture.Texture.EncodeToPNG());
			
			// border設定をするときに使うので残しておく
			textures[Path.GetFileNameWithoutExtension(assetPath)] = slicedTexture;
			
			// Refreshすると、さっき保存したpngが読み込まれる
			// delay入れないと稀にEditorごと落ちるので注意。原因不明。
			EditorApplication.delayCall += () => { AssetDatabase.Refresh(); };
		}
		
		void OnPreprocessTexture()
		{
			if(!assetPath.Contains("OnionRingSample/Editor/Out")) return;
			
			var fileName = Path.GetFileNameWithoutExtension(assetPath);
			if(!textures.ContainsKey(fileName)) return;
			
			// sliceしたtextureに対して、sprite boarderを設定する
			var slicedTexture = textures[fileName];
			var importer = assetImporter as TextureImporter;
			importer.spriteBorder = slicedTexture.Boarder.ToVector4();
			
			Debug.LogFormat("{0} Sliced!", fileName);
		}
	}
}
```

## 応用例

* psdから自動的に各レイヤーをpngで書きだして、OnionRingで9slice化する
* 9slice化したSpriteをまとめて自動的に[TexturePacker](https://www.codeandweb.com/texturepacker)にかける

などなど。

# あとがき

意見・感想・質問などなどは、[Twitter @kyubuns](http://twitter.com/kyubuns/) または ここのコメント欄 まで お気軽にどうぞ。
プルリクもお待ちしてます。

* このライブラリは[オインクゲームズ](oinkgms.com)の業務時間中に作ったものです。
* この記事は、[Unityアドベントカレンダー2015](http://qiita.com/advent-calendar/2015/unity) 19日目の記事の一部です。

# リポジトリ

[github: kyubuns/OnionRingUnity](github.com/kyubuns/OnionRingUnity)
