## AnimeRx

[UniRx](https://github.com/neuecc/UniRx)ガンガン使ってる方向けのTweenアニメーションライブラリです。

## コンセプト

- アニメーションライブラリといいつつも、TransformやGameObjectは握らず、  
素直に IObservable(float,Vector2,Vector3)を操作するライブラリ。  
(下記サンプル内の"Combine"や"Advanced - Circle"を見ていただければ柔軟さがわかると思います。)
- Easingは一通り実装済み。
- 「aからbにn秒で移動」だけでなく「aからbに速度vで移動」の指定が可能。
- スケジューラーも自分で書けるので「Time.time」「Time.unscaledTime」だけでなく独自のタイマーで動かすことが可能。

## リポジトリ

https://github.com/kyubuns/AnimeRx

## 基本

- ```Anime.Play``` で ```IObservable<Vector3>``` を準備
- ```SubscribeToPosition(cube)``` などを使ってtransformに代入

## サンプル

### Start

![sample1](https://user-images.githubusercontent.com/961165/35796308-7d0512aa-0a9f-11e8-9c66-c1dceeeafb72.gif)

(-5,0,0)から(5,0,0)へ、秒速4mで移動。

```csharp
Anime.Play(new Vector3(-5f, 0f, 0f), new Vector3(5f, 0f, 0f), Motion.Uniform(4f))
    .SubscribeToPosition(cube);
```

SubscribeToPositionは以下と同義です。

```csharp
    .Subscribe(x => cube.transform.position = x);
```

他にもSubscribeToLocalPosition, SubscribeToLocalScaleや、
```IObservable<float>``` を取るSubscribeToPositionXなどなど諸々準備してあります。

### Method Chain

![sample2](https://user-images.githubusercontent.com/961165/35796309-7d2bbdf6-0a9f-11e8-8fe1-acef944a36c0.gif)

(-5,0,0)から(5,0,0)へ移動した後、(0,3,0)に等速で移動。
メソッドチェーンでガンガン次の動きを書いていけます。

```csharp
var animator = Motion.Uniform(5f);
Anime.Play(new Vector3(-5f, 0f, 0f), new Vector3(5f, 0f, 0f), animator)
    .Play(new Vector3(0f, 3f, 0f), animator)
    .SubscribeToPosition(cube);
```

### Easing

![sample3](https://user-images.githubusercontent.com/961165/35796312-7d518662-0a9f-11e8-99e4-e5943212d966.gif)

EaseOutQuadで2秒かけて移動。
[Easing](http://easings.net/)は一通り実装済み。

```csharp
Anime.Play(new Vector3(-5f, 0f, 0f), new Vector3(5f, 0f, 0f), Easing.EaseOutQuad(TimeSpan.FromSeconds(2f)))
    .SubscribeToPosition(cube);
```

### Wait

![sample10](https://user-images.githubusercontent.com/961165/35796321-7e3ff25c-0a9f-11e8-956a-d85aa2a30e48.gif)

移動した後、1秒まって再度動き出す。

```csharp
Anime.Play(new Vector3(-5f, 0f, 0f), new Vector3(0f, 0f, 0f), Easing.EaseOutExpo(TimeSpan.FromSeconds(2f)))
    .Wait(TimeSpan.FromSeconds(1f))
    .Play(new Vector3(5f, 0f, 0f), Easing.EaseOutExpo(TimeSpan.FromSeconds(2f)))
    .SubscribeToPosition(cube);
```

### Path

![sample4](https://user-images.githubusercontent.com/961165/35796313-7d772fc0-0a9f-11e8-91c5-4780b43a5b4f.gif)

指定したpositionに順番に移動。

```csharp
var positions = new[]
{
    new Vector3(-5f, 0f, 0f),
    new Vector3(0f, 3f, 0f),
    new Vector3(5f, 0f, 0f),
    new Vector3(0f, -3f, 0f),
    new Vector3(-5f, 0f, 0f),
};

Anime.Play(positions, Easing.EaseInOutSine(TimeSpan.FromSeconds(6f)))
    .SubscribeToPosition(cube);
```

### Combine

![sample5](https://user-images.githubusercontent.com/961165/35796315-7d9e024e-0a9f-11e8-908c-f35b4326ee42.gif)

x, y, zの各座標を別々にアニメーションさせて合成。

```csharp
var x = Anime.Play(-5f, 5f, Easing.EaseInOutSine(TimeSpan.FromSeconds(3f)));

var y = Anime.Play(0f, 3f, Easing.EaseInOutSine(TimeSpan.FromSeconds(1.5f)))
    .Play(0f, Easing.EaseInOutSine(TimeSpan.FromSeconds(1.5f)));

var z = Anime.Stay(0f);

Observable.CombineLatest(x, y, z)
    .SubscribeToPosition(cube);
```

### Extensions

![sample6](https://user-images.githubusercontent.com/961165/35796317-7dc739de-0a9f-11e8-8aef-599e7e325efc.gif)

cube.transform.positionから(3,3,0)へ移動。

```csharp
cube.transform.position
    .Play(new Vector3(3f, 3f, 0f), Easing.EaseOutBack(TimeSpan.FromSeconds(2f)))
    .SubscribeToPosition(cube);
```

### Advanced - Circle

![sample8](https://user-images.githubusercontent.com/961165/35796318-7dedcb62-0a9f-11e8-907c-e0ee65298b17.gif)

IObservble<float>を円運動に変換。

```csharp
Anime.Play(0f, Mathf.PI * 2f, Easing.EaseOutCubic(TimeSpan.FromSeconds(3f)))
    .Select(x => new Vector3(Mathf.Sin(x), Mathf.Cos(x), 0.0f))
    .Select(x => x * 3f)
    .SubscribeToPosition(cube);
```

### Advanced - Timing

![sample9](https://user-images.githubusercontent.com/961165/35796319-7e1568d4-0a9f-11e8-8e08-28ff53093e8c.gif)

WhenAllを使ってアニメーションのタイミングを合わせる。

```csharp
var leftCube1 = Anime
    .Play(new Vector3(-5f, 0f, 0f), new Vector3(-0.5f, 0f, 0f), Easing.Linear(TimeSpan.FromSeconds(2.5f)))
    .DoToPosition(cube);

var rightCube1 = Anime
    .Play(new Vector3(5f, 0f, 0f), new Vector3(0.5f, 0f, 0f), Easing.EaseOutCubic(TimeSpan.FromSeconds(1f)))
    .DoToPosition(cube2);

var leftCube2 = Anime
    .Play(new Vector3(-0.5f, 0f, 0f), new Vector3(-0.5f, 3f, 0f), Easing.EaseOutCubic(TimeSpan.FromSeconds(1f)))
    .DoToPosition(cube);

var rightCube2 = Anime
    .Play(new Vector3(0.5f, 0f, 0f), new Vector3(0.5f, 3f, 0f), Easing.EaseOutCubic(TimeSpan.FromSeconds(1f)))
    .DoToPosition(cube2);

Observable.WhenAll(leftCube1, rightCube1)
    .ContinueWith(Observable.WhenAll(leftCube2, rightCube2))
    .Subscribe();
```

### AnimationCurve

![sample11](https://user-images.githubusercontent.com/961165/35796322-7e6983e2-0a9f-11e8-807c-ff3a2967a2cf.gif)

UnityEngine.AnimationCurveを利用して移動。

```csharp
Anime.Play(new Vector3(-5f, 0f, 0f), new Vector3(5f, 0f, 0f), Motion.From(curve, TimeSpan.FromSeconds(3f)))
    .SubscribeToPosition(cube);
```

### HPゲージが徐々に減る

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">AnimeRxを使うと、よくある「HPゲージが徐々に減る」ってやつもEasingでいい感じにアニメ付けながら簡単に作れる <a href="https://t.co/WW9lzrPyQg">https://t.co/WW9lzrPyQg</a> <a href="https://t.co/zgrPoxnMOd">https://t.co/zgrPoxnMOd</a> <a href="https://twitter.com/hashtag/Unity?src=hash&amp;ref_src=twsrc%5Etfw">#Unity</a> <a href="https://t.co/7jJKZPKSi4">pic.twitter.com/7jJKZPKSi4</a></p>&mdash; きゅぶんず (@kyubuns) <a href="https://twitter.com/kyubuns/status/960672588010733568?ref_src=twsrc%5Etfw">2018年2月6日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## あとがき

リポジトリ
https://github.com/kyubuns/AnimeRx

unitypackageはこちら
https://github.com/kyubuns/AnimeRx/releases

ご意見ご感想バンバンお待ちしております。
[@kyubuns](https://twitter.com/kyubuns)
