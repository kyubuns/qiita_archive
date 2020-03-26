弊社オインクゲームズから先日リリースした[1000m Zombie Escape!(以下1000mZ)](http://1000mz.jp)におけるゲームフローの制御方法について紹介します。

# はじめに

今回は、Unityの公式チュートリアルである[Tanks! Tutorial(Asset Store)](https://www.assetstore.unity3d.com/jp/#!/content/46209)のゲームフロー制御部分を参考にしています。
こちらの解説は、[テラシュールブログ](http://tsubakit1.hateblo.jp/entry/2015/10/16/223009)さんで紹介されてます。
（椿さんいつもお世話になってます！）
こちらを先に読んでおいてもらえると、これから出てくるコードが理解しやすいと思います。

# ゲームの流れ

1000mZは、1プレイ数分で終わるカジュアルゲームです。
具体的なゲームフローは以下のようになっています。

<img width="762" alt="gameflow3.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/43fd226e-8376-29f1-3fb8-4af61184c714.png">

メインフローは、タイトル画面->ゲームプレイ画面->フィニッシュ画面となっていて、
そこに「どこからでもタイトル画面に戻れる」という特徴を兼ね備えています。

# オレオレpub/subライブラリ "Kuchen"

オレオレpub/subライブラリ、その名も"Kuchen"を開発しました。
[詳しい紹介はこちらをご覧ください。](http://qiita.com/kyubuns/items/75735a0ccafc376d2b5d)
ゲームフロー制御のコードに出てくるので、先に紹介します。

```KuchenSample.cs
using UnityEngine;
using Kuchen;

public class KuchenSample : MonoBehaviour
{
	public void Start()
	{
		this.Subscribe("SampleTopic", (topic, arg) => {
			Debug.Log("sample topic: " + (string)arg);
		});

		// 別のGameObjectからでもOK
		this.Publish("SampleTopic", "abcde");
	}
}

// 実行結果
// sample topic: abcde
```

以上のコードが、Kuchenで出来る(ほぼ)全てです。
特定の文字列(今回は"SampleTopic")を、Subscribeしておけば、誰かがその文字列をPublishした時に呼ばれる。という単純なものです。
これを応用すると、以下のような事ができます。

```KuchenCoroutineSample.cs
using System.Collections;
using UnityEngine;
using Kuchen;

public class KuchenCoroutineSample : MonoBehaviour
{
	public IEnumerator Start()
	{
		Debug.Log("SampleTopicが送信されるまで待つよ。");
		yield return this.WaitForMessage("SampleTopic");
		Debug.Log("SampleTopicが呼ばれたよ！");
	}
}
```

このように、コルーチン内でイベントが送信されるのを待つことが出来ます。

## いやいや、Action使ってメソッドを登録すればいいじゃん

（と思わなかった方は読み飛ばして頂いて大丈夫です。）
このKuchenのメリットは2つあります。

* 発行する相手を知らなくていい
* 寿命管理を自分でしなくていい

1つ目は、さっきのコードを見た頂ければわかると思うので説明は省きます。
2つ目ですが、KuchenでSubscribeしたイベントは、GameObjectがDestroyされたタイミングで自動的に外されるようになっています。
なので、いちいちOnDestroyで参照を外したりする必要はありません。

# 具体的なコードと解説

話を戻して、具体的なゲームフロー制御の解説に入ります。
先に完成形のコードを張り、その後にコメント形式で解説をいれます。
（コードは説明のために簡略化してあり、ゲームに使っているそのままではありません。）

```GameFlow.cs
public void Start()
{
	StartCoroutine(MainFlow());
	StartCoroutine(NextGame());
}

public IEnumerator MainFlow()
{
	yield return StartCoroutine(LoadMap());
	yield return StartCoroutine(LoadCharacter());
	Loading.Finish();

	yield return this.WaitForMessage("Tap");

	this.Publish("Start");
	yield return this.WaitForMessage(new string[]{"Dead", "Goal"});
	this.Publish("Finish", player.IsAlive);

	this.Publish("WindowOpen.Result");
}

public IEnumerator NextGame()
{
	yield return this.WaitForMessage("NextGame");
	Loading.Start();
	SceneManager.LoadScene("Main");
}
```

## データロード

```GameFlow.cs
// public IEnumerator MainFlow()
	yield return StartCoroutine(LoadMap());
	yield return StartCoroutine(LoadCharacter());
	Loading.Finish();
```

まず、マップのロードおよびキャラクターモデルのロードをします。
ロードに何フレームかかっても大丈夫なように、ロード完了を待ってから次に進みます。
ロードが完了したら、Loading.Finish()で画面を覆っているローディング表示を外します。

## ゲーム開始まで

```GameFlow.cs
// public IEnumerator MainFlow()
	yield return this.WaitForMessage("Tap");
	this.Publish("Start");
```

"Tap"を待っている間、画面は以下のようになっています。
<img width="200" alt="IMG_3240.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/6459/59c1f4bf-510f-cce3-2b5d-acd041f567c7.png">

ユーザーはキャラクター選択ボタンも押せるし、オプション画面も押せます。
ボタン以外の箇所をタップすると、ゲームが開始されます。
これは、背面に画面全体を覆うようにボタンが置いてあり、そのボタンを押すと"Tap"イベントが飛ぶようになっています。

<img width="400" alt="Main_unity_-_client_-_iPhone__iPod_Touch_and_iPad__OpenGL_4_1_.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/e8379197-bc22-803c-807d-523ad24b337e.png">

そして、"Tap"のイベントが受け取ったら、自ら"Start"というイベントを発行します。
UIやプレイヤーキャラクター、周りのゾンビなどは、この"Start"イベントを受け取ることで動き始める仕組みになっています。

## ゲーム終了

```GameFlow.cs
// public IEnumerator MainFlow()
	yield return this.WaitForMessage(new string[]{"Dead", "Goal"});
	this.Publish("Finish", player.IsAlive);
	this.Publish("WindowOpen.Result");
```

"Dead", "Goal"のイベントが発行されたらゲーム終了です。
名前から想像できる通りのタイミングで発行されます。
"Finish"を発行することで、ゲームの終了を各ゲームオブジェクトに伝えると同時に、
"WindowOpen.Result"を発行することで、リザルト画面（以下の画像の画面）を表示しています。

<img width="200" alt="IMG_3241_2.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/56884e02-0349-0574-0a7f-0b209fe0f24c.png">

ちなみに、ウィンドウを表示するスクリプトは以下の様にワイルドカードを使いイベントを受信しています。

```WindowOpener.cs
this.Subscribe("WindowOpen.*", (topic, arg) => {
	var windowName = topic.Split('.')[1];
	// {windowName}を表示する
});
```

## 次のゲームへ

```GameFlow.cs
public void Start()
{
	StartCoroutine(MainFlow());
	StartCoroutine(NextGame());
}

// (MainFlow省略)

public IEnumerator NextGame()
{
	yield return this.WaitForMessage("NextGame");
	Loading.Start();
	SceneManager.LoadScene("Main");
}
```

MainFlowとNextGameは同時に走っています。
これは「いつでもタイトル画面に戻れる」ことを実現するためです。
同時に走っているので、いつ"NextGame"が発行されてもLoading.Startで画面転換の演出を行い、再びMainシーンをリロードすることが出来ます。

例えば、先ほど貼ったフィニッシュ画面での再挑戦ボタンを押した時などに、この"NextGame"イベントが発行されています。
<img width="200" alt="IMG_3241_3.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/436c176f-d500-13c7-dd2a-1115fb079006.png">


# あとがき

今回の1000mZでは、自分で作ったpub/subライブラリを利用してゲーム制御を行ってみました。
[Kuchenについてはこちらをご覧ください。](http://qiita.com/kyubuns/items/75735a0ccafc376d2b5d)
ゲームフロー制御にコルーチンを使う今回の方法は、あんまり大規模なゲームでは難しくなるかなーとも思いつつ、今回の規模ではちょうどよい方法でした。
上記のコードでは簡略化のため省いていますが、制御を一時停止するチュートリアルなどの遷移も綺麗に入れることが出来ました。

## 宣伝

[1000m Zombie Escape](http://1000mz.jp)をよろしくお願いします！


# 1000mZにおける〜シリーズ

["1000m Zombie Escape!"におけるゲームフロー制御方法](http://qiita.com/kyubuns/items/98b2127e01d9baf62fe7)
["1000m Zombie Escape!"における多言語対応手法](http://qiita.com/kyubuns/items/9cf5684bffc4103e944f)

