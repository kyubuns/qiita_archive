Unity界隈で9sliced spriteと呼ばれている9patchだか9sliceだかの画像を
自動で生成してくれるrubyのモジュールを作りました。
(この記事では9sliced spriteって呼びます。)

9sliced spriteって何？って人は[この辺](https://helpx.adobe.com/jp/fireworks/kb/7092.html)を参照してください。

# リポジトリ

https://github.com/kyubuns/onion_ring

# 何が出来るの？

こういう画像をつっこむと
![before.png](https://raw.githubusercontent.com/kyubuns/onion_ring/master/sample/before.png)

こういう画像が出てきます。
![after.png](https://raw.githubusercontent.com/kyubuns/onion_ring/master/sample/after.png)

さらに、[23, 0, 21, 0]というborderの値を得られます。
（left, top, right, bottom)
左から23px, 右から21pxまでを使って、その間を伸ばしてね。っていう指定です。

こういう画像をつっこむと
![before.png](https://qiita-image-store.s3.amazonaws.com/0/6459/2ca24278-be41-0a19-8803-bb231ab30fee.png)

こうなります。
![after.png](https://qiita-image-store.s3.amazonaws.com/0/6459/0df36154-50e3-dd20-f5e4-d1ebfa37a881.png)


# 使い方

めんどくさいのでGemとかになってないです。
要望があれば・・・

```sample.rb
border = OnionRing::run('before.png', 'after.png')
```
これだけ。

# Unity実用例

ここからUnityの話をします！

```sample.rb
# このスクリプトまともに動くか試してないので察してください。
slice_borders = []
slice_borders.push(['after1', OnionRing::run('before1.png', 'after1.png')])
slice_borders.push(['after2', OnionRing::run('before2.png', 'after2.png')])
# さらにここでafter1.png, after2.pngを、TexturePackerでまとめて、hoge_atlas.pngに保存とかすると便利。

slice_borders_text = slice_borders.map{|line| line.flatten.join(',')}.join("\n")
File.write("hoge_atlas_border.txt", slice_borders_text)
cp hoge_atlas_border.txt UnityProject/Assets/Resources/Hoge/
```

こんなかんじに、小さくした画像 + borderの値をテキストファイルに書いておきます。
そして、UnityのResources以下とか、まあ適当な場所にコピーもします。

```AtlasBorderSetter.cs
using UnityEditor;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class BorderSetter : AssetPostprocessor
{
	public override int GetPostprocessOrder() { return 200; } // TexturePackerが100
	
	static void OnPostprocessAllAssets (string[] importedAssets, string[] deletedAssets, string[] movedAssets, string[] movedFromAssetPaths)
	{
		foreach (var asset in importedAssets)
		{
			if(!asset.EndsWith("_border.txt")) continue;
			string pathToTexture = asset.Replace("_border.txt", ".png");
			if(File.Exists(pathToTexture)) AssetDatabase.ImportAsset(pathToTexture, ImportAssetOptions.ForceUpdate);
		}
	}
	
	void OnPreprocessTexture ()
	{
		TextureImporter importer = assetImporter as TextureImporter;

		string pathToData = assetPath.Replace(".png", "_border.txt");
		if (File.Exists(pathToData)) updateSpriteMetaData(importer, pathToData);
	}
	
	static void updateSpriteMetaData (TextureImporter importer, string pathToData)
	{
		string[] dataFileContent = File.ReadAllLines(pathToData);
		var borders = new Dictionary<string, Vector4>();
		foreach(var line in dataFileContent)
		{
			var elements = line.Split(',');
			var name = elements[0];
			var left = int.Parse(elements[1]);
			var top = int.Parse(elements[2]);
			var right = int.Parse(elements[3]);
			var bottom = int.Parse(elements[4]);
			borders[name] = new Vector4(left, bottom, right, top);
		}
		
		var metaList = new List<SpriteMetaData> ();
		for(int i = 0; i<importer.spritesheet.Length; ++i)
		{
			var sprite = importer.spritesheet[i];
			sprite.border = borders[sprite.name];
			metaList.Add(sprite);
		}
		
		importer.spritesheet = metaList.ToArray();
	}
}
```

後は、↑のスクリプトをUnityのEditor以下に置いておくと、自動的にborderの設定までしてくれます！

# リポジトリ

https://github.com/kyubuns/onion_ring

# あとがき

自動的に9slice sprite画像を生成してくれるツールを探したんですけど、どうも見当たらなかったので自分で作っちゃいました。
意見・要望などは[twitter](http://twitter.com/kyubuns)や、ここのコメント欄までお気軽にどうぞ！
ruby力低いのでコードへのつっこみもお待ちしてます。

# おまけ

<blockquote class="twitter-tweet" lang="ja"><p lang="ja" dir="ltr">. <a href="https://twitter.com/kyubuns">@kyubuns</a> が自動 9 slicer を作ってくれたんだけど、この画像がこうなって、目から鱗である (ちなみに正しい) <a href="https://t.co/sJVNvxmtVo">https://t.co/sJVNvxmtVo</a> <a href="http://t.co/Ug7V3QqmxN">pic.twitter.com/Ug7V3QqmxN</a></p>&mdash; Yoshihiro Shindo (@beinteractive) <a href="https://twitter.com/beinteractive/status/599153712959860739">2015, 5月 15</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
