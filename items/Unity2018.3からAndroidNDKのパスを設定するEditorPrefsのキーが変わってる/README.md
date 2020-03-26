AndroidSDK/NDKのパス、突然ビルドマシンからパスが消えて「SDKが見つかりません」って言われたりするので、
毎回EditorPrefsに設定してたものの、2018.3にしてから「NDKが見つかりません」ってたまに言われるようになったので調べてみたらキーが変わっていた。

Unity2018.2以前

```csharp
EditorPrefs.SetString("AndroidSdkRoot", "/path/to/android/sdk");
EditorPrefs.SetString("AndroidNdkRoot", "/path/to/android/ndk");
EditorPrefs.SetString("JdkPath", "/path/to/jdk");
```

Unity2018.3以降

```csharp
EditorPrefs.SetString("AndroidSdkRoot", "/path/to/android/sdk");
EditorPrefs.SetString("AndroidNdkRootR16b", "/path/to/android/ndk");
EditorPrefs.SetString("JdkPath", "/path/to/jdk");
```

今後はNDKバージョン上がるたびにパスが変わっていくということだろうか
