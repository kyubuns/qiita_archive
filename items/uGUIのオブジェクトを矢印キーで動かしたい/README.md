NGUIはUIWidget選択時にRectモードだと矢印キーでposition移動できるので
uGUIでも再現してみた。
Unity5用。

```CanvasRendererMover.cs
using UnityEngine;
using UnityEditor;
using System.Collections;
 
[CustomEditor(typeof(CanvasRenderer))]
public class CanvasRendererMover : Editor
{
	static int hint = "WidgetHash".GetHashCode();
	void OnSceneGUI()
	{
		if(UnityEditor.Tools.current != Tool.Rect) return;
 
		var e = Event.current;
		int id = GUIUtility.GetControlID(hint, FocusType.Keyboard);
		var type = e.GetTypeForControl(id);
		if(type != EventType.keyDown) return;
 
		if(e.keyCode == KeyCode.UpArrow) Move(target, new Vector3(0.0f, 1.0f), e);
		else if(e.keyCode == KeyCode.RightArrow) Move(target, new Vector3(1.0f, 0.0f), e);
		else if(e.keyCode == KeyCode.DownArrow) Move(target, new Vector3(0.0f, -1.0f), e);
		else if(e.keyCode == KeyCode.LeftArrow) Move(target, new Vector3(-1.0f, 0.0f), e);
	}
 
	void Move(object obj, Vector3 v, Event e)
	{
		var g = obj as CanvasRenderer;
		g.transform.localPosition += v;
		e.Use();
	}
}
```
