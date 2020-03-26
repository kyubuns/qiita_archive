AssetPostProcessor内で処理した場合は勝手に保存されている。たぶん。
これは、AssetPostProcessor外で処理するときの話です。

エディタ拡張でTextureImporter.spritesheetをスクリプトから変更した時、

```
EditorUtility.SetDirty(importer);
EditorUtility.SaveAssets();
AssetDatabase.SaveAssets();
```

ではだめで

```
EditorUtility.SetDirty(importer);
AssetDatabase.ImportAsset(texturePath, ImportAssetOptions.ForceUpdate);
```

する必要がある。
spritesheet以外のプロパティは、こんなことしなくても保存されてる気がする。

---

唐突に関係ない話します。
uGUIのTextでBitmapFontを使うと描画位置がズレるの、ググっても同士が見つからなくて困ってます。
何か知ってる方情報ください。
（Unity5.3.1p3にて確認）

<img width="911" alt="fo1.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/d8419325-7a79-fe03-bb7b-b850f048d69b.png">

<img width="918" alt="fo2.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/7a371a6d-f95b-0328-fe7a-017372e52a99.png">
