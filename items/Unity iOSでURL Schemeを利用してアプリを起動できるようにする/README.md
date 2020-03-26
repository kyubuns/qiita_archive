# 2014/11/27追記

Unityのどこかのバージョンからか、出力するInfo.plistの「スペースの量」が変化していて↓の方法使えなくなってます。
`<dict>`を`  <dict>`にすると治るのですが、やっぱり素直に
PostProcessでPlistBuddy使って追加する方が安全そうです。

```
/usr/libexec/PlistBuddy -c 'Add :CFBundleURLTypes array' Info.plist
/usr/libexec/PlistBuddy -c "Add CFBundleURLTypes:0:CFBundleURLSchemes array" Info.plist
/usr/libexec/PlistBuddy -c "Add CFBundleURLTypes:0:CFBundleURLSchemes:0 string appname" Info.plist
```

# やってること

PostProcessでXCodeProject(正確には、その中のInfo.plist)にurl schemeを登録するだけ。

# 前書き

AndroidはAndroidManifest.xmlに書くだけなのでググればでます。
iOSは何故かググってもPostprocessBuildPlayerでshellscript書いたりpython書いたりruby書いたり、
みんな自由だな〜と思ったのでc#で完結するコードを書いてみました。
shellscriptを使うと、こんな置換みたいな無理矢理な方法ではなくPlistBuddyという便利な奴を使えるので、それはそれでありだと思います。
PostprocessBuildPlayer使っちゃうとデバッグがとてもめんどくさい（Consoleに出ない）というのがマイナスポイントなので個人的には使いたくないです。
結果的に生まれる物は一緒なので、お好きな物をどうぞ。

# やりかた

↓のスクリプトをEditor以下に放り込めばOK。
[前にもまとめました](http://d.hatena.ne.jp/kyubuns/20140615/1402825919)が、別にAssets/Editorでなくても、Assets/HogeFuga/Editor以下でも、Assets/KotoriTyuntyun/Editor以下でもEditor以下なら何でもOKです。

```lang:BuildPostProcessor.cs
using System.IO;
using UnityEngine;
using UnityEditor;
using UnityEditor.Callbacks;

public class BuildPostProcessor
{
	[PostProcessBuild]
	public static void OnPostProcessBuild(BuildTarget target, string xcodeProjectPath)
	{
		if(target != BuildTarget.iPhone) return;
		AddUrlScheme(xcodeProjectPath, "hogefugapiyo");
	}

	private static void AddUrlScheme(string xcodeProjectPath, string scheme)
	{
		var plistPath = Path.Combine(xcodeProjectPath, "Info.plist");
		var info = File.ReadAllText(plistPath);
		var beforeText = "<plist version=\"1.0\">\n<dict>"; // どこかのバージョンから <plist version=\"1.0\">\n  <dict>になっています。対応した方使ってください。
		var afterText = string.Format("<plist version=\"1.0\">\n<dict>\n<key>CFBundleURLTypes</key><array><dict><key>CFBundleURLSchemes</key><array><string>{0}</string></array></dict></array>", scheme);

		info = info.Replace(beforeText, afterText);
		File.WriteAllText(plistPath, info);
	}
}
```

# ちなみに

いつも罠にかかるんですが、URL Schemeは_(アンダーバー)が使えません。
これ、ビルドは通るんですけどアプリ審査提出時の自動テストで弾かれるので気づきにくいです。

# 関連記事

[Unity iOSで自動的に画像リソースを追加する](http://qiita.com/kyubuns/items/8e5b56e51091aa5a48ba)
[Unity iOSで独自の*.frameworkを自動的に追加する](http://qiita.com/kyubuns/items/bce1e6f060d3ceeb9811)
