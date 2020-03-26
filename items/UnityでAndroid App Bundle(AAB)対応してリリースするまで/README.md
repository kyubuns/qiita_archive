丁寧に書いてる記事ではなく、同じ問題で困ってる人が見ると良い感じに分かる記事です。

## 署名する

- [keystoreを作る](https://qiita.com/ptkyoku/items/3fb1ae542ff04423c781)
- パスワードは保存されないのでビルド時によしなに設定する。

```csharp
PlayerSettings.Android.keystoreName = ~~~;
PlayerSettings.Android.keystorePass = ~~~;
PlayerSettings.Android.keyaliasName = ~~~;
PlayerSettings.Android.keyaliasPass = ~~~;
```

## このリリースは Google Play の 64 ビット要件に準拠していません

- 開発中はビルド早くしたかったのでIL2CPPを切っていた。
- これは単純にIL2CPP有効にしてARM64をターゲットに入れれば良い。

```csharp
PlayerSettings.SetScriptingBackend(BuildTargetGroup.Android, ScriptingImplementation.IL2CPP);
PlayerSettings.Android.targetArchitectures = AndroidArchitecture.ARM64 | AndroidArchitecture.ARMv7;
```

## APK が最適化されていません

> 解決策:
> Android App Bundle を使用してデバイスの設定に合わせて自動的に最適化するか、複数の APK を使って自分で管理します。

### 分割APK

分割APK(`PlayerSettings.Android.useAPKExpansionFiles`)でいけるならそれでいいか、とビルドしてみたものの同じ警告が出てダメだった。理由は不明。

### .aabを作る

- ビルドする。
- これで一件落着。

### GooglePlayConsoleにあげる

- と思ったら、まだ終わっていない。
- 作ったaabファイルをGooglePlayConsoleにアップロードすると「Googleでアプリ署名鍵の管理、保護を行ってください」とエラーを吐かれて弾かれる。
- 「アプリの署名」、3択の正解は「Java Keystoreから鍵をエクスポートしてアップロードする」
- `java -jar pepk.jar --keystore=(Unityから吐いたkeystore) --alias=*** --output=***.pem --encryptionkey=***` で作ったpemをあげる。
