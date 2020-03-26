UnityRecorder(com.unity.recorder)はEditorでしか使わない上、fccore.bundleが無いって言ってiOSビルド時にコケるので勝手に省くようにした。
Unity2019.1.7で確認。

```csharp
using System;
using UnityEditor;
using UnityEditor.Build;
using UnityEditor.Build.Reporting;
using UnityEditor.PackageManager;
using UnityEngine;

public class RemoveUnityRecorderOnBuild : IPreprocessBuildWithReport
{
    public int callbackOrder => 0;

    public void OnPreprocessBuild(BuildReport report)
    {
        var request = Client.Remove("com.unity.recorder");
        for (var i = 0; i < 100; ++i)
        {
            if (request.Status != StatusCode.InProgress) break;
            System.Threading.Thread.Sleep(100);
        }
        if (request.Status != StatusCode.Success)
        {
            throw new Exception($"{request.Status} / {request.Error?.message}");
        }

        AssetDatabase.Refresh();
    }
}
```

AssetDatabase.Refreshを呼び出した時点で"An AssetDatabase Refresh is pending after a successful Package Manager request. You should not call AssetDatabase.Refresh at this time because it can lead to import-related errors."って怒られますが、
AssetDatabase.Refreshが無いとパッケージが入ったままビルドされます。
謎。
