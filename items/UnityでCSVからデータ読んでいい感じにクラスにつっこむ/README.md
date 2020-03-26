# まえがき

マッパーってやつだと思います。たぶん。

[C#のリフレクションを使いモデルクラスを動的に初期化する](http://qiita.com/yonestra/items/976d575fed5a4997a3a4)
この記事にめっちゃお世話になりました！！
というか、半分ぐらいコピペです。

# 実現したいこと

こんな感じのcsvファイルを用意して

```lang:EnemyMaster.csv
ID,Name,Hp,Power
slime,スライム,10,1
bat,こうもり,5,2
```

たったこれだけで

```lang:EnemyMasterTable.cs
using UnityEngine;
using System.Collections;

public class EnemyMasterTable : MasterTableBase<EnemyMaster>
{
  private static readonly string FilePath = "EnemyMaster";
  public void Load() { Load(FilePath); }
}

public class EnemyMaster : MasterBase
{
  public string ID { get; private set; }
  public string Name { get; private set; }
  public int Hp { get; private set; }
  public int Power { get; private set; }
}
```

```lang:Hoge.cs
var enemyMasterTable = new EnemyMasterTable();
enemyMasterTable.Load();
foreach(var enemyMaster in enemyMasterTable.All)
{
    Debug.Log(enemyMaster.Name);
}
```

こんなことが出来るといいな！！！
と思って作りました。

# 中身

```lang:MasterTableBase.cs
using UnityEngine;
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;

public class MasterTableBase<T> where T : MasterBase, new()
{
  protected List<T> masters;
  public List<T> All { get { return masters; } }

  public void Load(string filePath)
  {
    var text = ((TextAsset)Resources.Load(filePath, typeof(TextAsset))).text;
    text = text.Trim().Replace("\r", "") + "\n";
    var lines = text.Split('\n').ToList();

    // header
    var headerElements = lines[0].Split(',');
    lines.RemoveAt(0); // header

    // body
    masters = new List<T>();
    foreach(var line in lines) ParseLine(line, headerElements);
  }
  
  private void ParseLine(string line, string[] headerElements)
  {
    var elements = line.Split(',');
    if(elements.Length == 1) return;
    if(elements.Length != headerElements.Length)
    {
      Debug.LogWarning(string.Format("can't load: {0}", line));
      return;
    }

    var param = new Dictionary<string, string>();
    for(int i=0;i<elements.Length;++i) param.Add(headerElements[i], elements[i]);
    var master = new T();
    master.Load(param);
    masters.Add(master);
  }
}

public class MasterBase
{
  public void Load(Dictionary<string, string> param)
  {
    foreach(string key in param.Keys) SetField (key, param[key]);
  }

  private void SetField(string key, string value)
  {
    PropertyInfo propertyInfo = this.GetType().GetProperty(key, BindingFlags.Public | BindingFlags.NonPublic | BindingFlags.Instance);

    if(propertyInfo.PropertyType == typeof(int))         propertyInfo.SetValue(this, int.Parse(value), null);
    else if(propertyInfo.PropertyType == typeof(string)) propertyInfo.SetValue(this, value, null);
    else if(propertyInfo.PropertyType == typeof(double)) propertyInfo.SetValue(this, double.Parse(value), null);
    // 他の型にも対応させたいときには適当にここに。enumとかもどうにかなりそう。
  }
}
```

# あとがき

便利なんですけど、そこそこ重いと思う（計測はしてない）のでお気を付けて。
iOS動作検証済み

# 宣伝

CSVを簡単にUnity上から確認できる[CSV Viewer](https://www.assetstore.unity3d.com/jp/#!/content/18424)をAssetStoreに出してるのでよければぜひぜひ。

![Dungeon_unity_-_chocolate_-_iPhone__iPod_Touch_and_iPad.png](https://qiita-image-store.s3.amazonaws.com/0/6459/5dd19404-367a-95b0-2daa-8477bd0f1b26.png)
