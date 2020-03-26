# AnimeTask

Task(async/await)ガンガン使ってる方向けのTweenアニメーションライブラリです。
[AnimeRx](https://qiita.com/kyubuns/items/761235f59febe923bf99)のTask版です。

![46568998-56470e80-c989-11e8-8798-c168a1c6b494.gif](https://qiita-image-store.s3.amazonaws.com/0/6459/97081543-ff93-1228-e92e-24ae7cdb7578.gif)

## リポジトリ

Unity2017.1から動きます。(.net4.6を有効にする必要があります。)
Unity2018.3bでも動作確認済みです。
[kyubuns/AnimeTask](https://github.com/kyubuns/AnimeTask)

## コンセプト

- Taskだと順番にアニメーション処理がかけて便利！

```csharp
await Task.WhenAll(
    Anime.PlayTo(Easing.Create<Linear>(new Vector3(5f, -2f, 0f), 2f), TranslateTo.LocalPosition(cube1)),
    Anime.PlayTo(Easing.Create<OutCubic>(new Vector3(5f, 0f, 0f), 2f), TranslateTo.LocalPosition(cube2))
);

await Task.Delay(TimeSpan.FromSeconds(2.0));

await Task.WhenAll(
    Anime.PlayTo(Easing.Create<Linear>(new Vector3(-5f, -2f, 0f), 2f), TranslateTo.LocalPosition(cube1)),
    Anime.PlayTo(Easing.Create<OutCubic>(new Vector3(-5f, 0f, 0f), 2f), TranslateTo.LocalPosition(cube2))
);
```

- 値計算と値代入を切り分けて考えることで色んなアニメーションを色んなデータに使える。
- Easingは一通り実装済み。
  - Linear
  - InQuad, OutQuad, InOutQuad
  - InCubic, OutCubic, InOutCubic
  - InQuart, OutQuart, InOutQuart
  - InQuint, OutQuint, InOutQuint
  - InSine, OutSine, InOutSine
  - InExpo, OutExpo, InOutExpo
  - InCirc, OutCirc, InOutCirc
  - InElastic, OutElastic, InOutElastic
  - InBack, OutBack, InOutBack
  - InBounce, OutBounce, InOutBounce

## サンプル

### Basic

`(-5f, 0f, 0f)` から `(5f, 0f, 0f)` へ2秒かけて移動する。

```csharp
await Anime.Play(
    Easing.Create<Linear>(new Vector3(-5f, 0f, 0f), new Vector3(5f, 0f, 0f), 2f),
    TranslateTo.LocalPosition(cube)
);
```

### PlayTo

PlayToを利用すると、現在地から移動する。

```csharp
await Anime.PlayTo(
    Easing.Create<Linear>(new Vector3(-5f, 3f, 0f), 2f),
    TranslateTo.LocalPosition(cube)
);
```

### Easing

EasingのInCubicを利用して移動する。

```csharp
await Anime.PlayTo(
    Easing.Create<InCubic>(new Vector3(-5f, 3f, 0f), 2f),
    TranslateTo.LocalPosition(cube)
);
```

### TranslateTo.Action

TranslateTo.Actionを利用すると、アニメーションした値を自由に使用出来る。

```csharp
await Anime.Play(
    Easing.Create<Linear>(0, 100, 2f),
    TranslateTo.Action<float>(x => Debug.Log(x))
);
```
