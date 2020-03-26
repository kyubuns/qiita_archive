設定を追加してみました。
こちらより最新のソースコードを落として頂き、
https://raw.githubusercontent.com/kyubuns/yogurt/master/ExportLayersToFile.jsx
`fileName = "{layer_name}.png" // 保存名`
となっている箇所を
`fileName = "{file_name}{index}.png" // 保存名`
と変更することで、ファイル名01.pngのような形式で出力されるようになります。
また、`folder = ""`という出力先を指定する設定も追加しました。
こちらを、デスクトップに出力するなら`folder = "~/Desktop"`のように指定すると保存先指定がでなくなります。
