Unity2018.3からPrefabWorkflowが新しくなりました。
これにより[4年前から待ち望んでいた](https://qiita.com/kyubuns/items/5741e5281f4bb8de656c)Prefab in Prefabが実現したり、Prefab編集用シーンがすぐに開けるようになったりともろもろ最高です。
詳細はこちら。
https://blogs.unity3d.com/jp/2018/06/20/introducing-new-prefab-workflows/

この変更に伴い、.prefabデータのフォーマットが大幅に変更になりました。
普通に作業している分には特に気にすることは無いのですが、コイツがたまに悪さをします。
具体的にはUnityのキャッシュがない状態から（2018.2以前からのキャッシュがあると上手くいく。）、2018.2以前のフォーマットのPrefabを含んだiOS向けAssetBundleを生成すると、iOS実機で読み込んだ時にだけ全てのコンポーネントが剥がれたPrefabが読み込まれる等です。他にもキャッシュがない状態からAssetBundleを生成したらコケるなど。

まあ前置きは置いといて、そんなときに「じゃあプロジェクト内の全てのPrefabを2018.3のフォーマットにしよう」と思うわけです。
ReimportAllしても変更は入りません。
ということでスクリプトを書きました。

```csharp
using UnityEditor;
using UnityEngine;

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
                var path = "Assets" + file.ToString().Replace(Application.dataPath, "");
                Debug.Log(path);
                var go = PrefabUtility.LoadPrefabContents(path);
                PrefabUtility.SaveAsPrefabAsset(go, path);
                PrefabUtility.UnloadPrefabContents(go);
            }
        }
    }
}
```
