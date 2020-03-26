ビルド用のスクリプトを別のリポジトリに置いといて一元管理！みたいなことをしている時に便利。

```ruby
private_lane :import_builder_to_unity do |options|
  require 'tmpdir'
  Dir.mktmpdir("test_clone") do |tmp_path|
    clone_folder = File.join(tmp_path, "r")
    url = "git@github.com:***/***.git"
    sh("GIT_TERMINAL_PROMPT=0 git clone '#{url}' '#{clone_folder}' --depth 1")

    FileUtils.cp("#{clone_folder}/Test.sh", ".")
  end
end
```
