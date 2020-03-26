タイトルでオチてます。

が、それだけじゃ寂しいので、rubyで書いたコードも貼っておきます。
コードからドキュメントを自動生成する方法は様々あると思うので、
ドキュメントがdocument.mdというファイルに保存されている体で。

```rb
require 'qiita'

namespace :hoge do
  desc "hogeのドキュメントをqiitaにアップロード"
  task :upload do
    client = Qiita::Client.new(access_token: "{アクセストークン}", team: "{チーム名}")
    body = File.read("document.md")
    client.patch("/api/v2/items/{更新する記事のID}", title: '{タイトル}', body: body)
  end
end
```

これ作った後に、githubのwikiで良かったんじゃ・・・って思ったんですけど考えないことにします。
