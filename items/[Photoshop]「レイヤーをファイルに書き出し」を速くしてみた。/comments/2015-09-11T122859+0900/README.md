コメントありがとうございます。
上記2つをオプションの機能として追加しました。
ファイルをダウンロードしなおした後、ファイル先頭の

```
// 設定

enable_trim = true // トリミングするかどうか
output_invisible_layer = false // 非表示のレイヤーも書き出し
```

の設定を

```
enable_trim = false // トリミングするかどうか
output_invisible_layer = true // 非表示のレイヤーも書き出し
```

に書き換えて使っていただくと、トリミングせずに非表示レイヤーも書き出す設定になります。

https://github.com/kyubuns/yogurt/blob/master/ExportLayersToFile.jsx
