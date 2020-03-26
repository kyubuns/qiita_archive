めっちゃシンプルなコードだけど毎回毎回書いてる気がしたのでここにメモしておく。

```TexturePostprocessor.cs
using UnityEngine;
using UnityEditor;
using System;
using System.Collections.Generic;

public sealed class TexturePostprocessor : AssetPostprocessor
{
	Dictionary<string, Action<TextureImporter>> formats = new Dictionary<string, Action<TextureImporter>>{
		{ "Assets/Resources/Hoge/Fuga", HogeFuga },
		{ "Assets/Resources/A/B/C/Icon", Icon },
	};

	static void HogeFuga(TextureImporter target)
	{
		target.textureFormat = TextureImporterFormat.Automatic16bit;
	}

	static void Icon(TextureImporter target)
	{
		target.mipmapEnabled = false;
		target.textureFormat = TextureImporterFormat.AutomaticTruecolor;
	}

	void OnPreprocessTexture()
	{
		TextureImporter target = assetImporter as TextureImporter;
		foreach(var a in formats)
		{
			var path = a.Key;
			var func = a.Value;
			if(!target.assetPath.StartsWith(path)) continue;
			
			Debug.LogFormat("TextureImportProcessor:{0}", target.assetPath);
			func(target);
			break;
		}
	}
}
```
