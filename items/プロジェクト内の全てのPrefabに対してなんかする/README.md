## ああ〜〜プロジェクト内の全てのPrefabのTextComponentの色変えて〜

```csharp
using System.IO;
using UnityEditor;
using UnityEngine;
using UnityEngine.UI;

namespace Sandbox
{
    public static class ReimportPrefab
    {
        [MenuItem("Sandbox/ReimportPrefab")]
        public static void Run()
        {
            var directory = new System.IO.DirectoryInfo(Application.dataPath);
            var files = directory.GetFiles("*.prefab", System.IO.SearchOption.AllDirectories);

            foreach (var file in files)
            {
                var path = Path.Combine("Assets", file.ToString().Replace(Application.dataPath, ""));

                var go = PrefabUtility.LoadPrefabContents(path);
                foreach (var text in go.GetComponentsInChildren<Text>(true))
                {
                    text.color = Color.blue;
                }
                PrefabUtility.SaveAsPrefabAsset(go, path);
                PrefabUtility.UnloadPrefabContents(go);
            }
        }
    }
}

```

## 関連

- プロジェクト内の全てのPrefabを2018.3フォーマットに変換する
    - https://qiita.com/kyubuns/items/e558d50d239922061a48
