Unity2018.3から

```csharp
[SerializeField] private GameObject hoge;
```

というコードが `[CS0649] Field 'Piyo.hoge' is never assigned to, and will always have its default value null` という警告を吐くようになりました。
警告の詳細は[こちら(CS0649)](https://docs.microsoft.com/ja-jp/dotnet/csharp/misc/cs0649)。

冷静に考えると、今まで警告が出てなかった方がおかしいのですが、
(Unityがコンパイラに手を入れていたため出ていなかった)
なにはともあれ、ちゃんと初期化して警告を消してあげましょう。

```csharp
[SerializeField] private GameObject hoge = default;
```

.NET4.xなら、以下のsedで一気に消せます。

```shell
sed -i '' -e "s/\[SerializeField\] private \([^;]*\)/\[SerializeField\] private \1 = default/g" ***/*.cs
```

Rider使いの方、Riderでは「そんな初期化要らないよ！消しちゃえ消しちゃえ！」ってRiderが `= default` 部分を消してきます。
くそ〜と思いながらRedundantDefaultMemberInitializerをDO_NOT_SHOWにしましょう。
