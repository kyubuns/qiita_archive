make_changelog_from_jenkinsは認証とかが面倒だなーと思ったので
jenkinsが勝手に設定しておいてくれる ENV['GIT_PREVIOUS_SUCCESSFUL_COMMIT'] を利用する。

```ruby
private_lane :make_changelog do |options|
  changelog_from_git_commits(
    between: [ENV['GIT_PREVIOUS_SUCCESSFUL_COMMIT'] || "HEAD^", "HEAD"],
    pretty: "- %s (%an)",
    merge_commit_filtering: 'exclude_merges',
  )
end
```
