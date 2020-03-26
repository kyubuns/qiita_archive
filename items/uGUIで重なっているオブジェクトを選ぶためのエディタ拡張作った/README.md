Greenという半透明のオブジェクトが最も手前にあり、WhiteとRedに重なっている状態。
SceneViewでRedを選択しようとしても、最も手前にあるGreenが選ばれてしまう。
![4.png](https://qiita-image-store.s3.amazonaws.com/0/6459/43da0aed-34fa-f9e4-2042-5ec5ed509c48.png)

そこで、この拡張ウィンドウを表示しておくと、
SceneView上でタップした位置にあるゲームオブジェクトを拡張ウィンドウに全て表示。
![2.png](https://qiita-image-store.s3.amazonaws.com/0/6459/cf194543-441b-0ddd-16e1-e009b7d0b272.png)

ボタンをクリックすると、対応したオブジェクトが選択状態になる。
![3.png](https://qiita-image-store.s3.amazonaws.com/0/6459/48492576-d9ae-2cb2-af1b-af9ada1b8101.png)

それだけ。

```csharp:UguiSelector.cs
using UnityEditor;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.EventSystems;
using System;
using System.Linq;
using System.Collections;
using System.Collections.Generic;

public class UGUISelector : EditorWindow
{
	static bool initialized = false;
	static List<RaycastResult> raycastResults;
	public static bool RayCasting { get; private set; }
	
	[MenuItem("Window/uGUI Selector")]
	static void Open()
	{
		GetWindow<UGUISelector>();
	}
	
	void OnInspectorUpdate()
	{
		if(!initialized && EditorApplication.isPlaying)
		{
			if(SceneView.onSceneGUIDelegate != OnSceneGUI)
			{
				SceneView.onSceneGUIDelegate += OnSceneGUI;
				EditorApplication.playmodeStateChanged += OnPlayModeChanged;
			}
			initialized = true;
		}
	}
	
	void OnGUI()
	{
		if(!EditorApplication.isPlaying)
		{
			GUILayout.Label("Play mode only");
			return;
		}
		if(raycastResults == null) return;
		
		foreach(var obj in raycastResults)
		{
			if(obj.gameObject == null) return;
			
			var path = string.Join(".", obj.gameObject.GetParentNames().Reverse().Take(3).Reverse().ToArray());
			if(GUILayout.Button(path)){
				if(obj.gameObject == null) return;
				Selection.activeGameObject = obj.gameObject;
			}
		}
	}
	
	void OnSceneGUI(SceneView sceneView)
	{
		if(!EditorApplication.isPlaying) return;
		if (Event.current.type == EventType.MouseDown)
		{
			Vector2 mousePos = Event.current.mousePosition;
			mousePos.y = sceneView.camera.pixelHeight - mousePos.y;
			Vector3 position = sceneView.camera.ScreenPointToRay(mousePos).origin;
			
			var pointer = new PointerEventData(EventSystem.current);
 			pointer.position = position;
			
			raycastResults = new List<RaycastResult>();
 			EventSystem.current.RaycastAll(pointer, raycastResults);
			
			Repaint();
		}
	}
 
 	void OnPlayModeChanged()
 	{
		raycastResults = new List<RaycastResult>();
		Repaint();
	}
	
	void OnDestroy()
	{
		SceneView.onSceneGUIDelegate -= OnSceneGUI;
		EditorApplication.playmodeStateChanged -= OnPlayModeChanged;
		initialized = false;
	}
}
```
