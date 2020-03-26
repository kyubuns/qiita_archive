生成したXCodeプロジェクトの下、Unity-iPhone/Images.xcasset/LINE.imageset

というディレクトリは出来てるでしょうか？

その下に、LINE_icon01.pngというファイルがあれば大丈夫なはずです。

そもそもディレクトリが存在しなければ、

var sourceDirNames = Directory.GetDirectories(Application.dataPath, "*.imageset", SearchOption.AllDirectories);
の行でディレクトリが引っかからないところにあるのかもしれません。

Debug.Logなどで出力してみて確かめてみてください。
