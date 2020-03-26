Unity用Pub/Subライブラリ、その名も"[Kuchen](https://github.com/kyubuns/Kuchen)"を公開したのでご紹介します。
読み方はクーヘンです。バウムクーヘンのクーヘンです。

## なにこれ？

コンセプトは、**小さいゲーム開発プロジェクトの開発速度を上げるライブラリ** です。
小さなプロジェクトというのが大切で、個人でゲームを作ってる！とか、プロトタイプをさくっと作りたい！とかいう時に向いてると思います。

## 何ができるの？

誰かがSubscribeして、誰かがPublishする。それだけです。


```csharp
this.Subscribe("SampleTopic", () => { Debug.Log("baum") });
```
誰かがSubscribeして、

```csharp
this.Publish("SampleTopic");
```
誰かがPublishする。
こうすれば、先ほど登録していた `` () => { Debug.Log("baum") }) `` が呼ばれます。

### いやいや、Action使ってメソッドを登録すればいいじゃん

（と思わなかった方は読み飛ばして頂いて大丈夫です。）
Kuchenのメリットは2つあります。

* 発行する相手を知らなくていい
* 寿命管理を自分でしなくていい

1つ目は、さっきのコードを見た頂ければわかると思うので説明は省きます。
2つ目ですが、KuchenでSubscribeしたイベントは、GameObjectがDestroyされたタイミングで自動的に外されるようになっています。
なので、いちいちOnDestroyで参照を外したりする必要はありません。

## 使用例

こんなの何に使えるの？？と、頭の上に？マークが浮かんでいると思うので、具体的な使用例を紹介していきます。

### 例えば、エフェクトや効果音

Kuchenで実現できることの1つに「気軽にクラスの責任を分けられる」というものがあります。
「Spaceキーを押したらプレイヤーキャラクターが攻撃する」処理を書く時、

```どこかの入力管理クラス.cs
if(Input.GetKeyDown(KeyCode.Space)) Publisher.Publish("Attack");
// MonoBehaviourであれば、this.Publish("Attack"); でも同じ意味。
```

入力管理クラスは、入力を管理するだけ。
プレイヤーへの参照を握る必要はありません。

```プレイヤークラス.cs
this.Subscribe("Attack", () => {
  // 当たり判定とか敵へのダメージ処理
});
```

```エフェクト.cs
this.Subscribe("Attack", (_, pos) => {
  // 攻撃エフェクトを表示
});
```

```効果音.cs
this.Subscribe("Attack", () => {
  // 攻撃の効果音を再生
});
```

このように、1つのイベントを複数人が受け取ることで、「送る側は何も考えずに送るだけ」「受け取る側は、自分の作業に必要なイベントを受け取るだけ」が実現できます。
また、プレイヤークラス、エフェクトクラス、効果音クラスは、それぞれ参照を握る必要はありません。
ここに、「攻撃時だけカメラがアップになる！」みたいな処理を入れようと思えば、カメラクラスでもAttackをSubscribeすれば良いだけです。

### 例えば、UI

沢山あるUIのボタン1つ1つに、UnityEditorのInspectorからOnClickの処理を設定していく・・・というのはなかなか苦痛なんですよね。
または、（うちの場合がそうなんですけど）psdからuGUIのGameObjectを自動生成してる！とかいう場合。

```Extension.cs
foreach(var button in root.GetComponentsInChildren<Button>())
{
  var buttonName = button.gameObject.name;
  button.onClick.AddListener(() => {
    Publisher.Publish(string.Format("OnClick.{0}", buttonName));
  });
}
```

このようにすれば、root以下のButton全てにPublishイベントを設定できます。
受け取り側はこんな感じ。

```ボタンイベント.cs
this.Subscribe("OnClick.HogeButton", () => { Debug.Log("HogeButtonが押されたよ！"); });
this.Subscribe("OnClick.FugaButton", () => { Debug.Log("FugaButtonが押されたよ！"); });
```

### 例えば、コルーチンによるゲームフロー制御

[1000mZ](http://1000mz.jp)では、ゲームフローの制御にKuchenを使っています。
詳しくは[こちらの記事](http://qiita.com/kyubuns/items/98b2127e01d9baf62fe7)をご覧ください。

## 応用例

ここからは、実際こんな使い方したことないけど、まあ使えるな〜みたいな例を書いていきます。

### 例えば、生存確認

少し上で「送る側は何も考えずに送るだけ」と書きましたが、
Publishの返り値として、「何人が受け取ったか」がintで帰ってくるので、生存確認なんかも出来ます。

```Enemy.cs
this.Subscribe("Ping", () => {});
```

```全員死んでるかな？.cs
if(Publisher.Publish("Ping") == 0) { /* 敵は全滅。*/ }
```

## ほか

他にも良い使用例募集中です！コメントにでも書いてってください！

## リポジトリ

具体的な使い方はREADME.mdに書いてあります。
https://github.com/kyubuns/Kuchen

## あとがき

前々から作ろう作ろうと思っていたライブラリがやっと形になりました。
Kuchenは、制約の緩いPub/Subライブラリです。
制約の強さには好みがあると思いますが、そこは一旦作りやすいもの、ということで抑えました。
「各クラスが何に責任を持つか」を明確にし、「自分の責任でないことは他人（他クラス）に任せる。」「誰が誰の参照を持っているかなんて知ったこっちゃない。」
ということを、めちゃくちゃ気軽に実現したい！と思っていて考えた結果、Kuchenが完成しました。
Kuchenを使うことで、特にプロジェクト初期の開発速度が上がると考えています。

さらに、このライブラリは、思想によって様々な使い方が出来ると思っています。
この記事だけでは、自分の思想は伝えきれていないと思うので、「ここまで読んだけど、何が良いのかわからなかった」という方もいらっしゃると思います。
伝えきれていないことを思い出せたら追記します。

## 最後に

ご意見、ご感想、PRなどなど待ってます。
[@kyubuns](http://twitter.com/kyubuns)
