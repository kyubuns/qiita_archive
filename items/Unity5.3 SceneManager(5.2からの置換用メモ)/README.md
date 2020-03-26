ググってもあんまり見当たらなかったので軽くまとめておく

[リファレンス SceneManager](http://docs.unity3d.com/530/Documentation/ScriptReference/SceneManagement.SceneManager.html)

```Hoge.cs
// SceneManagerはUnityEngine.SceneManagement名前空間！
using UnityEngine.SceneManagement;
```

| 5.2 | 5.3 |
|:--------------------------|:--------------------------|
|Application.LoadLevel("Uduki")|SceneManager.LoadScene("Uduki")|
|Application.LoadLevelAdditive("Mio")|SceneManager.LoadScene("Mio", LoadSceneMode.Additive)|
|Application.loadedLevelName|SceneManager.GetActiveScene().name|

5.2からの単純移植はこんな感じ、Sceneクラスが増えてるので使うべきかも。
