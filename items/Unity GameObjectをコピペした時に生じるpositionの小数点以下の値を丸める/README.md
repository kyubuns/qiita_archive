# はじめに

Unityって、GameObjectをSceneViewでいじってても、小数点以下の動きはしないようになってます。
↑よくよく調べてみたら、NGUIの挙動でした。

もちろん、Inspectorから直接入力したら、小数点も入ります。
後、Shift押しながらドラッグしても小数点付きで移動します。
でも、まぁ普通は小数点入らないようになってます。

# でも

UnityでGameObjectを操作していて、positionに小数点が入ることがあります。
自分がよく引っかかるのは、GameObjectをコピー&ペーストした時。
（他にも条件あったら教えてください！）

うわーー、コピペしたゲームオブジェクトにだけpositionに小数点以下の値がー！！ってなります。
気持ち悪いから消します。

# スクリプト

これを、Editor以下につっこんでください。

```
using UnityEngine;
using UnityEditor;

[InitializeOnLoad]
public class PositionRounder
{
  static PositionRounder()
  {
    EditorApplication.hierarchyWindowChanged += OnHierarchyWindowChanged;
  }

  static void OnHierarchyWindowChanged()
  {
    foreach(var target in Selection.gameObjects)
    {
      var p = target.transform.localPosition;
      p.x = Mathf.Round(p.x * 1000.0f)/1000.0f;
      p.y = Mathf.Round(p.y * 1000.0f)/1000.0f;
      p.z = Mathf.Round(p.z * 1000.0f)/1000.0f;
      target.transform.localPosition = p;
    }
  }
}
```

