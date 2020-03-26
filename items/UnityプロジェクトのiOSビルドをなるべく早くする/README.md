# まえがき

Unityでゲームを作っていて、「開発チーム内に共有する開発者向けビルドをなるべく早くしたい！」というお話です。
リリースビルドには使わないほうが良いオプションが多数登場します。
UnityからXCodeプロジェクトを出力した後は、fastlaneを利用してビルドしているだけなので、実はUnityそんな関係ないです。
ここではfastlaneのコードとして紹介しますが、XCode上からでも同じ設定が出来るはずです。
「ここのパラメーターいじるともっと早くなるかも？」みたいな情報バンバンお待ちしております！

# 結論

プロジェクト規模やビルドマシンのスペックによってビルド時間は変わるので参考値でしかありませんが、
オプション無しで40分かかっていたビルドが、10分ちょっとで終わるようになりました。

```ruby
    build_ios_app(
      project: xcodeproj_path,
      scheme: "Unity-iPhone",
      configuration: "Release",
      export_method: "ad-hoc",
      export_options: {
        uploadBitcode: false,
        uploadSymbols: false,
        compileBitcode: false,
      },
      include_bitcode: false,
      include_symbols: false,
      xcargs: "IDEBuildOperationMaxNumberOfConcurrentCompileTasks=16 GCC_GENERATE_DEBUGGING_SYMBOLS=NO DEBUG_INFORMATION_FORMAT=dwarf ONLY_ACTIVE_ARCH=YES VALID_ARCHS=arm64 CC=\"ccacheclang\" GCC_PRECOMPILE_PREFIX_HEADER=NO",
    )
```

# 詳細

## debug symbolとbitcodeを生成しない。

debug symbolを生成しないのはクラッシュした時のログが見にくくなって悩むところですが、
ビルド時間がかなり軽減されたので生成しないことにしました。
なぜこのオプションが2段階になっているのかは謎です。

```ruby
      export_options: {
        uploadBitcode: false,
        uploadSymbols: false,
        compileBitcode: false,
      },
      include_bitcode: false,
      include_symbols: false,
```

`GCC_GENERATE_DEBUGGING_SYMBOLS=NO DEBUG_INFORMATION_FORMAT=dwarf`

## 並列ビルド

CPUのコア数とかと相談しながら決めてください。
盛り盛りMacProを使用しているので16並列にしています。

`IDEBuildOperationMaxNumberOfConcurrentCompileTasks=16`

## arm64向けビルドしか行わない

開発チーム内にarmv7を使っている人間がいなかったのでarm64だけに絞りました。
これも結構効きます。

`ONLY_ACTIVE_ARCH=YES VALID_ARCHS=arm64`

## ccacheを使う

ccacheの説明は省きますが、まあそりゃ使ったらビルド早くなるわなって感じなので使います。
CC=\"ccache clang\" って書けたら良かったんですけど実行時に死ぬので
```ccache clang $@``` とだけ書いたファイルを/usr/local/bin/ccacheclangみたいなところに置いておきます。

`CC=\"ccacheclang\"`
