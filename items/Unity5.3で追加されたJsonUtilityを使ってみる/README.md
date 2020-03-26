[リファレンスはこちら](http://docs.unity3d.com/530/Documentation/ScriptReference/JsonUtility.html)

[System.Serializable]を忘れててうまくパースされないという罠にかかったので一応メモ。

ちなみに
> JsonUtility: JsonUtility no longer supports non-user-defined UnityEngine.Object types. Use UnityEditor.EditorJsonUtility for them now.
> JsonUtility: JsonUtility.FromJson() no longer supports creating ScriptableObject instances. Only plain .NET classes and structs are supported now.
> https://unity3d.com/jp/unity/beta/unity5.3.0b2

Dictionaryは未対応っぽい。対応予定あるのかな・・・

```JsonTest.cs
using System;
using System.Collections.Generic;
using UnityEngine;


[Serializable]
public class Stage
{
	public string StageName;
	public List<Enemy> Enemies;
	private int privateValue1; // Serializeされないものはjsonに含まれない
	[SerializeField] private int privateValue2; // Inspectorと同じで、こうやればprivateだけどjsonに含まれる
}

[Serializable]
public class Enemy : ISerializationCallbackReceiver
{
	public string Name;
	public int Hp;
	public int Mp;

	public void OnBeforeSerialize()
	{
		Debug.Log("Serialize前にごにょごにょいじれる");
	}
	
	public void OnAfterDeserialize()
	{
		Debug.Log("Deserialize後にごにょごにょいじれる");
	}
}

public class JsonTest : MonoBehaviour
{
	void Start()
	{
		var json = Serialize();
		Deserialize(json);
	}
	
	string Serialize()
	{
		var enemy1 = new Enemy()
		{
			Name = "MonsterA",
			Hp = 10,
			Mp = 5,
		};
		
		var enemy2 = new Enemy()
		{
			Name = "MonsterB",
			Hp = 9,
			Mp = 6,
		};
		
		var enemy3 = new Enemy()
		{
			Name = "MonsterC",
			Hp = 8,
			Mp = 7,
		};
		
		var stage = new Stage()
		{
			StageName = "DummyStage",
			Enemies = new List<Enemy>{ enemy1, enemy2, enemy3 }
		};
		
		return JsonUtility.ToJson(stage);
	}
	
	void Deserialize(string json)
	{
		var stage = JsonUtility.FromJson<Stage>(json);
		Debug.LogFormat("StageName: {0}", stage.StageName);
		foreach(var enemy in stage.Enemies) Debug.LogFormat("Name: {0} | {1}/{2}", enemy.Name, enemy.Hp, enemy.Mp);
	}
}
```

最近[雑な更新](http://qiita.com/kyubuns/items/d7f8c26250a6744c508e)が多いんですけどアドベントカレンダー頑張るので許してください
