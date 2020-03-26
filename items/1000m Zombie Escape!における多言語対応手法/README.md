[1000m Zombie Escape!](http://1000mz.jp)(以下1000mZ)における8カ国語対応方法について紹介します。
※こちらの記事で紹介するのは技術的な内容であり、ローカライズそのものに関するあれやこれやの話はしません。

# はじめに

1000mZは「日本語」「英語」「ドイツ語」「中国語（簡体字）」「韓国語」「フランス語」「スペイン語」「イタリア語」の8カ国語に対応しています。
8カ国語対応するにあたって発生した問題点とその解決方法についてご紹介します。

# 前置き

## 「いい感じフォント」と「なんでもフォント」

1000mzでは、ゲームの世界観にあった「いい感じフォント」（こちらは英語やドイツ語しか対応していない）と、
日本語や韓国語、中国語を表示するための「なんでもフォント」の2つのフォントを使用しています。

## Unicodeは全て表示できないといけない！

「GameCenterのフレンド名を表示する」という機能があるため、
ゲーム内で使用する文字だけを表示できるようにしておく・・・ではなく、
全ての文字を表示する必要がありました。

# ドイツ語のASCIIコードは128-255に入ってる！

## IsAlphabet

「ラベルのテキストがアルファベットのみなら、"いい感じフォント"、違えば"なんでもフォント"を使う」という処理を行っています。
そのために、「テキストがアルファベットのみかどうか」を判定するメソッドを作りました。
説明を簡単にするため、1文字だけ判定しています。

```csharp
bool IsAlphabet(char c) { return (int)c < 127; }
```

## ドイツ語

ここで問題が発生しました。
「ドイツ語がなんでもフォントで表示されてしまう」という問題です。
ドイツ語やスペイン語にはアルファベットだけでなく、以下の様な文字が含まれています。
https://ja.wikipedia.org/wiki/%C3%9C

「ドイツ語の文字コードとかどうなってるんだよ・・・」と思って調べてみたところ、128~254の範囲に収まってくれているみたいです。
[ASCII 文字セット (128 ～ 255)](https://msdn.microsoft.com/ja-jp/library/cc392379.aspx)

よって、以下のように修正しました。
IsAlphabetはおかしいんですかね。まあこんな感じです。

```csharp
bool IsAlphabet(char c) { return (int)c < 255; }
```

# uGUIのTextエレメントを全部探して自動的に翻訳を割り当ててる話

ローカライズ対応は、ゲーム内に存在しているラベル全部書き換えないといけないってことなので、1つ1つ対応なんてやってられません。
パーッとやりましょう。

```csharp
foreach(var text in root.GameObject.transform.GetComponentsInChildren<Text>())
{
  text.text = Localizer.Get(text.name + text.transform.parent.name);
  // Localizer.Get("HogeButton.Text") -> "Hoge" みたいなのを返してくれるように良い感じのデータを作っておく
}
```

# フォント描画が重い！

実は、何でもかんでも描画できるようにDynamicフォントを使っていました。
さすがにシーン遷移時等のフォントの描画が重すぎるという判断になり、全てBitmapフォントに変えました。
「なんでもフォント」はBitmapのUnicodeに設定すればよかったのですが、
「いい感じフォント」には上であげたようにドイツ語なども必要なため、Custom setで設定しました。
<img width="452" alt="font1.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/76f1850f-c0b9-e3cd-d86f-16c1bb3ccd0b.png">

```
 !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~ ¡¢£¤¥¦§¨©ª«¬­®¯°±²³´µ¶·¸¹º»¼½¾¿ÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖ×ØÙÚÛÜÝÞßàáâãäåæçèéêëìíîïðñòóôõö÷øùúûüýþ
```

# フォントサイズがでかい！

さて、ここで上がってくるのはフォントのファイルサイズがでかい！問題です。
BitmapにしてUnicode全てを持つとなると、とてもテクスチャサイズがでかくなってしまい、フォントサイズを下げるとフォントが荒くなってしまう・・・
この問題をフォントを2つに分割することで解決しました。
「なんでもフォント - マスター用」「なんでもフォント - フレンド名用」

## なんでもフォント - マスター用

マスターに含まれている文字列（つまりゲーム内で使用する文字列）をフォントサイズ64ぐらいで持たせておきます。
こちらは限られて文字だけなので、文字数は少ないです。

## なんでもフォント - フレンド名用

こちらはUnicode全てをもたせます。
その代わりにフォントサイズは小さくしてしまいます。
そして、フレンド名はあまりでかく描画しないようにUIを少し修正してもらいました。

# 文字幅調整の話

「翻訳して貰ったテキストを見ると思ってたより長かった！」
ローカライズあるあるですね。
今回は、テキストラベルの幅にあわせて自動的にフォントサイズを調整してくれる機構を自作しました。
uGUIのTextにBestFitという機能はあるのですが、Bitmapフォントでは効かないんですよね。

```TextExtensions.cs
using UnityEngine;
using UnityEngine.UI;

namespace Baum
{
	public static class TextExtensions
	{
		public static void RescaleTextForBestFit(this Text text)
		{
			var rate = text.preferredWidth / (text.gameObject.GetComponent<RectTransform>().sizeDelta.x);
			text.rectTransform.localScale = Vector3.one * Mathf.Clamp01(1.0f / Mathf.Max(1.0f, rate));
		}
	}
}
```

ざっとこんな感じに。
横幅しか調整してくれないのでご注意ください。

# あとがき

このような困難を乗り越えて1000mzの多国語対応は行われています。
「こういうところはどうなってるの？」とか、「こうしたほうがスマートじゃない？」などなど、
ご意見ご感想は コメント欄や[twitter](http://twitter.com/kyubuns/)で受け付けております。

先日よりユニティちゃんコラボも開催中！
[1000m Zombie Escape!](http://1000mz.jp/)よろしくお願いします！

![56e77405f50b09351d00001d.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/74b87cd2-6aa4-c43a-58b7-42a29b1a74c3.jpeg)

# 1000mZにおける〜シリーズ

["1000m Zombie Escape!"におけるゲームフロー制御方法](http://qiita.com/kyubuns/items/98b2127e01d9baf62fe7)
["1000m Zombie Escape!"における多言語対応手法](http://qiita.com/kyubuns/items/9cf5684bffc4103e944f)
