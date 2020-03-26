ボタンの見た目そのままで、当たり判定だけ広げる方法 Unity5.3バージョン
つばきさんのコードを参考にしました。Unity5.3だとOnFillVBOがobsoleteって言われるので書き直し。
http://tsubakit1.hateblo.jp/entry/2015/01/30/213000

```GraphicCast.cs
using UnityEngine;
using System.Collections;
using UnityEngine.EventSystems;
using UnityEngine.UI;
#if UNITY_EDITOR
using UnityEditor;
#endif

namespace UnityEngine.UI
{
	public class GraphicCast : Graphic{
		
		protected override void OnPopulateMesh(VertexHelper v)
		{
			base.OnPopulateMesh(v);
			v.Clear();
		}
		#if UNITY_EDITOR
		
		[CustomEditor(typeof(GraphicCast))]
		class GraphicCastEditor : Editor
		{
			public override void OnInspectorGUI() {
			}
		}
		
		#endif
	} 
}
```

こんな感じの拡張メソッド書いて使ってます。

```HogeExtension.cs
public static void ExtendSize(this Button button, float scale)
{
	var graphicCastObject = new GameObject("GraphicCast");
	graphicCastObject.transform.SetParent(button.transform);

	var size = button.gameObject.GetComponent<RectTransform>().sizeDelta;
	var rect = graphicCastObject.AddComponent<RectTransform>();
	rect.localPosition = Vector3.zero;
	rect.localScale = Vector3.one;
	rect.sizeDelta = size * scale;
	var graphicCast = graphicCastObject.AddComponent<GraphicCast>();
	button.targetGraphic = graphicCast;
}
```
