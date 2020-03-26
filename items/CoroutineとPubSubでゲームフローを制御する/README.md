# はじめに

[1週間ゲームジャム お題「space」](https://unityroom.com/unity1weeks)を見て、
[宇宙で駐車スペースに駐車するゲーム](https://unityroom.com/games/space_parking)という物理演算ドタバタゲーを作ったので、
せっかくなら技術解説記事でも書こうと思い立った次第です。

# 利点

PubSubでフローを管理することで、クラス間の依存が少なく出来てサイコー！って感じです。

# MessageBrokerってなに？

[UniRx](https://www.assetstore.unity3d.com/#!/content/17276?aid=1100l3pRW)で提供されているPubSub機構です。
型ベースなのが非常に良いです。

* 参考 : [UniRxのMessageBrokerが便利という話](https://qiita.com/CST_negi/items/6f9e692df4e4847cf8a9)

# 本題へ

このゲームの流れはこうです。

* 最初の1秒は説明テキストを見せる（操作不可）
* 操作可能にする
* 何かしらのキーが押されたらゲームスタート
    * 時間計測開始
    * タイトル画面が消える
* 車が駐車スペースに止まったらゲーム終了
* ボタンを押したらリトライ

* **+ いつでもリトライ出来る**

## ゲームフロー管理マン

いきなりゲームフローを管理してるクラスです。
上に書いたゲームフローをそのままコードに落としたように綺麗に書けてます。素敵。

```csharp
public class GameFlow : MonoBehaviour
{
    public IEnumerator Start()
    {
        yield return new WaitForSecondsRealtime(1.0f);

        Controller.Enabled = true;

        yield return new WaitUntil(() => Controller.Any);

        var startTime = Time.time;
        MessageBroker.Default.Publish(new GameStart());

        yield return MessageBroker.Default.Receive<CarStay>().First().ToYieldInstruction();

        MessageBroker.Default.Publish(new GameFinish(Time.time - startTime));
    }

    public class GameStart
    {
    }

    public class GameFinish
    {
        public float Time { get; }

        public GameFinish(float time)
        {
            Time = time;
        }
    }

    // 車が駐車スペースに停車したら飛んでくる
    public class CarStay
    {
    }
}
```

## タイトル画面をふわぁ〜っと消す

ゲームが開始したら、タイトル画面（「↑あなた」とか「↑ここにとめる」とかテキストが出てるやつ）をふわぁ〜っと消します。
ゲームスタートが飛んできたら、CanvasGroupごとフェードアウト。
これもコルーチンで回します。非常にシンプル。

```csharp
public class Title : MonoBehaviour
{
	public IEnumerator Start()
	{
		yield return MessageBroker.Default.Receive<GameFlow.GameStart>().First().ToYieldInstruction();
		yield return StartCoroutine(GetComponent<CanvasGroup>().FadeOut());
		gameObject.SetActive(false);
	}
}
```

これはゲームフローとは関係ないのですが、ふわぁ〜っとCanvasGroupのalphaを0にするやつも便利なので置いときます。

```csharp
public static class CanvasGroupExtensions
{
    public static IEnumerator FadeOut(this CanvasGroup group)
    {
        var time = 0.0f;
        while (time < 1.0f)
        {
            group.alpha = Mathf.Lerp(1.0f, 0.0f, time);
            time += Time.deltaTime;
            yield return null;
        }
        group.alpha = 0.0f;
    }
}
```

## リトライはいつでも受け付けたい

リトライはゲームのフローに関係なく、いつでも突然呼び出したい。
そんな時は、普通にSubscribeしましょう。

```csharp
public class GameFlow : MonoBehaviour
{
    public IEnumerator Start()
    {
        MessageBroker.Default
            .Receive<NextGame>()
            .Subscribe(_ => SceneManager.LoadScene(SceneManager.GetActiveScene().name))
            .AddTo(this);

        yield return new WaitForSecondsRealtime(1.0f);

　　　　　〜あとはいっしょ〜
```

## ゲームが終了したら結果を表示する

GameFlow.StartでPublishした値、どこでも誰でも自由に受け取れます。

```csharp
[RequireComponent(typeof(Text))]
public class Result : MonoBehaviour
{
	public IEnumerator Start()
	{
		var finish = MessageBroker.Default.Receive<GameFlow.GameFinish>().First().ToYieldInstruction();
		yield return finish;
		GetComponent<Text>().text = $"きろく {finish.Result.Time}びょう";
	}
}
```

## ツイートボタンでも値を使いたい！

上記、Resultクラスとは何の依存も必要ありません。
こっちも勝手に値を待って、使えばいいだけ。

```csharp
[RequireComponent(typeof(Button))]
public class TweetButton : MonoBehaviour
{
    public IEnumerator Start()
    {
        var finish = MessageBroker.Default.Receive<GameFlow.GameFinish>().First().ToYieldInstruction();
        yield return finish;

        GetComponent<Button>().OnClickAsObservable().Subscribe(_ =>
        {
            Debug.Log($"{finish.Result.Time}秒だったよ！とツイート");
        });
    }
}
```

# まとめ

いかがでしょうか、GameFlowクラスでゲームの流れは全て制御していながら、GameFlowクラスからも、他のクラスからも、お互いに依存は1つもありません。
「ゲームクリアしたときのデバッグがしたい！」という時に、「GameFinishをPublishするボタン」を置いておけばゲームクリアしたことになる。というのも非常に便利です。
ご意見ご感想は、ここのコメント欄か[@kyubuns](https://twitter.com/kyubuns)までお気軽にどうぞ！

# 余談1

async/awaitを使えば、値を受け取るコードがもっとシンプルに書けて嬉しい。
https://gist.github.com/kyubuns/d0609398e71e16bb87a5451c72963391

# 余談2

PubSubは関係ないけれど、他にも依存を減らす工夫を紹介します。
キー入力をしてから、車が動いて炎のエフェクトが出るまでの流れです。
ちなみにエフェクトはAssetStoreから拾ってきた[CubeSpace - Effects](https://www.assetstore.unity3d.com/#!/content/74293?aid=1100l3pRW)を使っています。

```csharp
public static class Controller
{
    public static bool Enabled { get; set; }
    public static bool Up => Enabled && (Input.GetKey(KeyCode.UpArrow) || Input.GetKey(KeyCode.W));
    public static bool Left => Enabled && (Input.GetKey(KeyCode.LeftArrow) || Input.GetKey(KeyCode.A));
    public static bool Down => Enabled && (Input.GetKey(KeyCode.DownArrow) || Input.GetKey(KeyCode.S));
    public static bool Right => Enabled && (Input.GetKey(KeyCode.RightArrow) || Input.GetKey(KeyCode.D));
    public static bool Any => Up || Left || Down || Right;
}
```

```csharp
public class Handle : MonoBehaviour
{
    public BoolReactiveProperty Up { get; } = new BoolReactiveProperty();
    public BoolReactiveProperty Down { get; } = new BoolReactiveProperty();
    public BoolReactiveProperty Left { get; } = new BoolReactiveProperty();
    public BoolReactiveProperty Right { get; } = new BoolReactiveProperty();

    public void Update()
    {
        Up.Value = Controller.Up;
        Down.Value = Controller.Down;
        Right.Value = Controller.Right;
        Left.Value = Controller.Left;
    }
}
```

```csharp
public class Engine : MonoBehaviour
{
    [SerializeField] private float UpForce;
    [SerializeField] private float DownForce;
    [SerializeField] private float RightTorque;
    [SerializeField] private float LeftTorque;

    private Handle Handle;
    private RectTransform RectTransformCache;
    private Rigidbody2D RigidbodyCache;

    public void Awake()
    {
        Handle = GetComponent<Handle>();
        RectTransformCache = GetComponent<RectTransform>();
        RigidbodyCache = GetComponent<Rigidbody2D>();
    }

    public void Update()
    {
        if (Handle.Up.Value)　RigidbodyCache.AddForce(RectTransformCache.up * UpForce * Time.deltaTime, ForceMode2D.Force);
        if (Handle.Down.Value)　RigidbodyCache.AddForce(RectTransformCache.up * -DownForce * Time.deltaTime, ForceMode2D.Force);
        if (Handle.Right.Value)　RigidbodyCache.AddTorque(-RightTorque * Time.deltaTime, ForceMode2D.Force);
        if (Handle.Left.Value)　RigidbodyCache.AddTorque(LeftTorque * Time.deltaTime, ForceMode2D.Force);
    }
}
```

```csharp
[RequireComponent(typeof(Handle))]
public class CarParticle : MonoBehaviour
{
    [SerializeField] private Handle Handle;
    [SerializeField] private List<ParticleSystem> Front;
    [SerializeField] private List<ParticleSystem> Rear;
    [SerializeField] private List<ParticleSystem> Right;
    [SerializeField] private List<ParticleSystem> Left;

    public void Start()
    {
        Handle.Up.DistinctUntilChanged().Subscribe(x => SetParticleActive(Rear, x)).AddTo(this);
        Handle.Down.DistinctUntilChanged().Subscribe(x => SetParticleActive(Front, x)).AddTo(this);
        Handle.Right.DistinctUntilChanged().Subscribe(x => SetParticleActive(Right, x)).AddTo(this);
        Handle.Left.DistinctUntilChanged().Subscribe(x => SetParticleActive(Left, x)).AddTo(this);
    }

    private static void SetParticleActive(IEnumerable<ParticleSystem> list, bool active)
    {
        foreach (var particleSystem in list)
        {
            if (active) particleSystem.Play();
            else particleSystem.Stop();
        }
    }
}
```
