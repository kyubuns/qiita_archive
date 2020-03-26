先に、/Applications/Unityを/Applications/Unity5.3.1p1というように、後ろにバージョン番号を付けておくようにします。
そしたら以下のスクリプトで、ProjectSettings/ProjectVersion.txtが更新されたら勝手に変わるように出来ます。便利。

```
UnityVersion=`head -n 1 ProjectSettings/ProjectVersion.txt | cut -d " " -f 2-2`
/Applications/Unity${UnityVersion}/Unity.app/Contents/MacOS/Unity -batchmode -quit -projectPath . -logFile ./UnityBuildLog.txt
```

みんなはどんな方法でやってるんだろう？と疑問に思ったので、まずは自分のやってる方法を公開してみました。
こんなやり方でやってるよ！っていうのがあればコメントください！
