ユーザー入力の文字が入り得ないテキストは、出来るだけ綺麗に描画したい。
ということで、マスターデータから使うテキスト情報を拾ってきて、その文字列を含んだBitmapFontを作る。
スクリプト内ではcustomCharactersしか変えてないので、他の設定は先に手動で終わらせておくこと。

```UpdateFont.cs
[MenuItem("Hoge/Update Font")]
public static void UpdateFont()
{
	var path = "Assets/Font/***.ttf";
	var importer = AssetImporter.GetAtPath(path) as TrueTypeFontImporter;

	IEnumerable<char> allCharacters = "";

	// ↓ここでゴニョゴニョして必要な文字を列挙する
	foreach(var text in Hoge.Fuga.Texts)
	{
		// ゴニョゴニョ
		allCharacters += text;
	}

	allCharacters = allCharacters.Distinct().OrderBy(c => c);
	var charactersString = new String(allCharacters.ToArray()).Replace("\n", "");
			
	if(importer.customCharacters == charactersString) return;
	importer.customCharacters = charactersString;
	EditorUtility.SetDirty(importer);
	AssetDatabase.ImportAsset(path, ImportAssetOptions.ForceUpdate);
}
```
