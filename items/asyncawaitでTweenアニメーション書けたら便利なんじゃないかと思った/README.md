# はじめに

今年の2月に[AnimeRx](https://github.com/kyubuns/AnimeRx)というRxでTweenアニメーション書けるライブラリを作りました。
詳細は[こちら](https://qiita.com/kyubuns/items/761235f59febe923bf99)。
ですが、この半年の間でUnityのasync/awaitにも春がやってきました。
Rxで書けるのも便利だったんですけど、アニメーションってasync/awaitの方が向いてるんじゃね？と思ってTask版も作ってみたわけです。

# 使い方

[UnityPackageはこちらからダウンロード出来ます。](https://github.com/kyubuns/AnimeTask/releases)
Unity2018.3b3でしか挙動確認していません。

```csharp
using System;
using AnimeTask;
using AnimeTask.Easing;
using UnityEngine;
using UnityEngine.UI;

public class Development : MonoBehaviour
{
    [SerializeField] private Text text;

    public async void Start()
    {
        Debug.Log("Move Start");

        await Anime.Play(
            OutBack.Animation(new Vector3(-5f, 0f, 0f), new Vector3(5f, 0f, 0f), TimeSpan.FromSeconds(2f)),
            TranslateTo.LocalPosition(transform)
        );

        await Anime.Play(
            OutExpo.Animation(new Vector3(5f, 0f, 0f), new Vector3(5f, 3f, 0f), TimeSpan.FromSeconds(2f)),
            TranslateTo.LocalPosition(transform)
        );

        await Anime.Play(
            Linear.Animation(0, 100, TimeSpan.FromSeconds(2f)),
            TranslateTo.Text(text, "{0}pt")
        );

        await Anime.Play(
            Linear.Animation(0, 100, TimeSpan.FromSeconds(2f)),
            TranslateTo.Action<float>(x => Debug.Log(x))
        );

        Debug.Log("Move End");
    }
}
```

こんな感じで出来ます。2つ同時にやりたいときはTask.WhenAllとか使うと良い感じなんじゃないでしょうか。
