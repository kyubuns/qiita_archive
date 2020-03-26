iOS, Androidのクリップボードを扱う方法をググると、このアセットを入れて〜とか、ネイティブプラグインを〜とかいう記事が色々出てきますが、そんな時代は終わりました。
Unity2018.1以降ならこれでいけます。

```csharp
Debug.Log($"クリップボードのテキスト = {GUIUtility.systemCopyBuffer}");
GUIUtility.systemCopyBuffer = "新しいテキスト";
```

### 2017.4

https://docs.unity3d.com/ja/2017.4/ScriptReference/GUIUtility-systemCopyBuffer.html

`Note: iOS and Android do not support this feature.`
この時点ではサポートされてなかったのでネイティブプラグインを書く必要があった。

### 2018.1

https://docs.unity3d.com/ja/2018.1/ScriptReference/GUIUtility-systemCopyBuffer.html

`Note: tvOS and Tizen do not support this feature.`
tvOSとTizen以外はいけるようになった。

### 2019.1

https://docs.unity3d.com/ja/2019.1/ScriptReference/GUIUtility-systemCopyBuffer.html

`Note: tvOS does not support this feature.`
tvOSは未だ対応されず。
