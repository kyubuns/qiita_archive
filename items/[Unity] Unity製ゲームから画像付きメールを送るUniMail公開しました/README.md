Unity製ゲームから画像付きメールを送るライブラリ、"[UniMail](https://github.com/kyubuns/UniMail)"を公開したのでご紹介します。

# はじめに

今のところ画像付きメールをサポートしているプラットフォームは **iOSのみ** です。
Editor/Windows/OSX/Androidからも通常のメールは送れます。
サポートプラットフォームを増やすPullRequest募集中です。

# なにこれ？

Unity製ゲームから、画像付きメールを送れます。

<img width="300" alt="IMG_6439.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/6459/aebdcea5-b892-7541-25cd-9d091f0488ff.png"> → <img width="300" alt="IMG_6438.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/6459/ae022e64-497f-ffb1-cdb4-83def0c9a03b.png">

# 使い方

```csharp
using UnityEngine;
using UniMail;

public class Sample : MonoBehaviour
{
    public void Start()
    {
        Mail.Send("unimail@example.com", "subject", "body1\nbody2", ScreenShotPath);
    }
}
```

# あとがき

リポジトリは[こちら](https://github.com/kyubuns/UniMail)。
バグ報告は[twitter](https://twitter.com/kyubuns)か[githubのissue](https://github.com/kyubuns/UniMail/issues)にどうぞ。
