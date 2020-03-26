# はじめに

Prefab in Prefabは、Unityさんが公式で対応すると言っていますが、待ちきれなかったので作っちゃいました。
http://feedback.unity3d.com/suggestions/editor-nested-prefabs

Play時だけではなく、Edit時にも動作します！

バグ報告、ご意見、ご感想、お待ちしております。
http://twitter.com/kyubuns
http://forum.unity3d.com/threads/244380-FREE-OpenSource-Prefab-in-Prefab-for-Unity4-x

# どんな時に使うの？

1つのPrefabの中に、似たようなボタンを2つ入れたい。

![スクリーンショット_050914_100604_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/f5130710-5f3c-03fc-a00f-6a6556521655.jpeg)

コピーして作ってしまうと、「ボタンの背景素材を入れ替える」とかなったときにめんどくさいなぁ・・・
でも、今のUnityはPrefabの中にPrefabがあるときに、親のPrefabをApplyすると子が親のPrefabに吸収されてしまい、子のPrefabを独立したまま反映させることが出来ない。

などなど、これ汎用的に使いたいなぁ。という時に使います！

# どんなことが出来るの？

![スクリーンショット_050914_101456_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/e2bf262f-3568-ec5e-a1ef-cfe9cb3eeda6.jpeg)

これがPrefab in Prefabだ！

![スクリーンショット_050914_100945_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/b90ad583-3676-a240-b459-bec12405d04c.jpeg)

元となっているPrefabを変更すると・・・
（Applyした後に保存したタイミング）

![スクリーンショット_050914_101103_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/186188a1-e1d6-d96f-bd80-320efecba38d.jpeg)

Prefab in Prefabを利用している方にも反映される！

# どうやって使うの？

## 準備編
↓をcloneしてきて、
https://github.com/kyubuns/prefab_in_prefab

```shell-session
cp prefab_in_prefab/Assets/PrefabInPrefab your_project/Assets/
```

以上。

## 使い方

![スクリーンショット_050914_095834_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/2c4a906c-5d47-a18f-4621-f29ae5e61f9d.jpeg)
PrefabInPrefabコンポーネントを張り付けて、Prefabを設定するだけ。

# 同じPrefabで別の挙動をさせる

PrefabInPrefabコンポーネントがついているゲームオブジェクトについているコンポーネントは、
生成されたPrefabのinstanceにコピーされます。

NGUIのUIButtonを利用した例を紹介します。

WindowPrefabの中で、ButtonPrefabを使いたいとします。

↓は、WindowPrefab内のGameObjectです。
![スクリーンショット_050914_095957_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/a5299a41-7205-e392-300f-18f93a992e07.jpeg)
（「これらはコピーされる」って文字の上にPrefabInPrefabついてます。文字の入れ方が雑ですみません；；）

実行時には、コンポーネントがコピーされるため、↓のような状態になります。
![スクリーンショット_050914_100135_PM.jpg](https://qiita-image-store.s3.amazonaws.com/0/6459/77b80bd5-0d70-3ca0-b478-62c42565c2d8.jpeg)

ここのButtonTextコンポーネント(ButtonPrefabにつける)、SetButtonTextコンポーネント(WindowPrefabのButtonにつける。)は以下のような簡単なスクリプトです。

```csharp:ButtonText.cs
using UnityEngine;
using System.Collections;

public class ButtonText : MonoBehaviour {
	[SerializeField] UILabel label;
	public string text
	{
		set {
			label.text = value;
		}
		get {
			return label.text;
		}
	}
}
```

```csharp:SetButtonText.cs
using UnityEngine;
using System.Collections;

[ExecuteInEditMode]
public class SetButtonText : MonoBehaviour {
	[SerializeField] string text;
	
	void Start()
	{
		var buttonText = GetComponent<ButtonText>();
		if(buttonText != null) GetComponent<ButtonText>().text = text;
	}
}
```

このようにして、同じPrefabを使いつつ、別のテキストをラベルに設定することができます。
また、上記画像にあるようにUIEventTriggerを利用することにより、ボタンが押された時のイベントも別々に設定ができます。

（追記：PrefabInPrefab/Example/Scenes/ExampleにNGUIを使用しないサンプルを追加しました。）
