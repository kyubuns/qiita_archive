# Unity(実行ファイル)のパス取得

```ruby
def get_unity_path(project_path)
  unity_version = File.read("#{project_path}/ProjectSettings/ProjectVersion.txt").split(" ")[1].strip

  unity_path = "/Applications/Unity#{unity_version}"
  unity_path = "/Applications/Unity/Hub/Editor/#{unity_version}" if File.exists?("/Applications/Unity/Hub/Editor/#{unity_version}")

  return unity_path
end
```

# Unity上のメソッドを実行

```ruby
private_lane :unity do |options|
  unity_path = get_unity_path(options[:project_path])

  args = ""
  args += " -buildTarget #{options[:target]}" if options[:target]
  args += " #{options[:args]}" if options[:args]

  sh("#{unity_path}/Unity.app/Contents/MacOS/Unity -batchmode #{args} -nographics -quit -projectPath #{options[:project_path]} -executeMethod #{options[:method]} -logFile /dev/stdout")
end
```

```ruby
unity(
  project_path: project_path,
  target: "osx",
  method: "AssetBundles.BuildScript.BuildAssetBundles",
)
```

# Unityテスト実行

```ruby
private_lane :run_unity_editor_tests do |options|
  unity_path = get_unity_path(options[:project_path])

  sh("#{unity_path}/Unity.app/Contents/MacOS/Unity -batchmode -quit -runEditorTests -editorTestsResultFile #{options[:result_path]} -projectPath #{options[:project_path]} -logFile /dev/stdout")
end
```

# XCodeビルドを頑張る

```ruby
      xcargs = ""
      xcargs += " IDEBuildOperationMaxNumberOfConcurrentCompileTasks=16"
      xcargs += " GCC_GENERATE_DEBUGGING_SYMBOLS=NO DEBUG_INFORMATION_FORMAT=dwarf"
      xcargs += " ONLY_ACTIVE_ARCH=YES VALID_ARCHS=arm64"
      xcargs += " CC=\"ccacheclang\" GCC_PRECOMPILE_PREFIX_HEADER=NO" if File.exist?("/usr/local/bin/ccacheclang")

      build_ios_app(
        project: xcodeproj,
        scheme: "Unity-iPhone",
        codesigning_identity: codesigning_identity,
        configuration: "Debug",
        export_method: "ad-hoc",
        export_options: {
          signingStyle: "manual",
          compileBitcode: false,
          provisioningProfiles: {
            "#{options[:app_identifier]}": "#{provisining_uuid}"
          },
        },
        skip_profile_detection: true,
        include_bitcode: false,
        include_symbols: false,
        xcargs: xcargs,
      )
```
