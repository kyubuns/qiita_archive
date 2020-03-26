今までシェルスクリプトから「Unityを叩いてXCodeプロジェクトを吐かせる→fastlaneを呼び出してipa作る」としていたところを、
全部fastlane内にまとめられたほうが綺麗かも って思ったので作りました。
ProjectVersion.txtからUntiyVersionを取ってきて、それっぽいパスを実行してます。

```ruby
unity(
    project_path: "./Game",
    method: "Hoge.BuildiOSDev",
)
```

```ruby
private_lane :unity do |options|
  # unity versionを調べる
  unity_version = File.read("#{options[:project_path]}/ProjectSettings/ProjectVersion.txt").split(" ")[1].strip

  # 実行
  sh("/Applications/Unity#{unity_version}/Unity.app/Contents/MacOS/Unity -batchmode -quit -projectPath #{options[:project_path]} -executeMethod #{options[:method]} -logFile /dev/stdout")
end
```
