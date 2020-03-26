# PostProcessingPaletteSwap

https://github.com/kyubuns/PostProcessingPaletteSwap

## 出来ること

![output](../../img/1_f.gif)

- PostProcessでカラーパレットを変更できる！
- カラーパレットは最大256色まで対応
- [カラーのスプライトを特定ディレクトリに放り込んだら、勝手にカラーパレット対応してくれるエディタスクリプト](https://github.com/kyubuns/PostProcessingPaletteSwap/blob/master/Assets/Plugins/PaletteSwap/Sample/Editor/IndexColorConverter.cs)付き！

### カラーパレットのサンプル

![a.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/6459/8fb1eaf1-7c25-7d0f-e71e-170b96b3dbb0.png)

- 例えば8色のパレットなら、↑これを8px x 1pxで保存して突っ込めばオッケー！
- サンプルにもパレット入ってるんですけど、github上で見ると小さすぎて見えないのでめっちゃ拡大して見てください
  - https://github.com/kyubuns/PostProcessingPaletteSwap/tree/master/Assets/Plugins/PaletteSwap/Sample/Palettes

## 出来ないこと

- PostProcessでやってるので、こっちのキャラだけ色替え！みたいなのは（ちょっと工夫しないと）出来ません。あくまで画面全体が対象です。
