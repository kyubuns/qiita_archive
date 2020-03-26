# こんな古くさいクラスは嫌だ

`Dummy.Run()`を呼ぶと、`Dummy.Success`か`Dummy.Failed`のどっちかが呼ばれるぜ！みたいなロックなクラスがあるとします。

```csharp
public static class Dummy
{
    public static void Run()
    {
        Debug.Log("Run!");
        Observable.Timer(TimeSpan.FromSeconds(1))
            .Subscribe(x =>
            {
                if (Random.Range(0, 2) == 0) Success?.Invoke();
                else Failed?.Invoke("エラーメッセージ");
            });
    }

    public static event Action Success;
    public static event Action<string> Failed;
}
```

原始時代かよってツッコみたくなりますが、ネイティブプラグインとか書いてると仕方ないときがあるんですよね。

# まずは普通に使ってみる

```csharp
Dummy.Success += () => Debug.Log("成功!");
Dummy.Failed += error => Debug.Log($"失敗... {error}");

Dummy.Run();
```

いやもう購読解除も面倒だし、2回目呼んだ時とかどうなっちゃうの！？あーーー

# どう呼べたら嬉しいか

```csharp
var result = await DummyRunWrap();
if (result.Success) Debug.Log("成功!");
else Debug.Log($"失敗... {result.ErrorMessage}");
```

そうそう、令和のC#ってこうだよね。

# Taskにする

そこでこうしてこう。

```csharp
public class Result
{
    public bool Success { get; set; }
    public string ErrorMessage { get; set; }
}

public async UniTask<Result> DummyRunWrap()
{
    var resultTask = Observable.Amb(
        Observable.FromEvent(x => Dummy.Success += x, x => Dummy.Success -= x)
            .Select(x => new Result { Success = true, Error = null }),
        Observable.FromEvent<string>(x => Dummy.Failed += x, x => Dummy.Failed -= x)
            .Select(x => new Result { Success = false, Error = "message" })
    ).First().ToUniTask();

    Dummy.Run();
    return await resultTask;
}
```

こうして平和な2019年が訪れましたとさ。

# まだお気づきじゃない方へ

`Dummy.Run()` の結果が帰ってくる前にもう一度 `Dummy.Run()` を呼ぶと死にます。
