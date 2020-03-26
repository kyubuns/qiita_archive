ついに発売しました！
RPGツクールMV！！！ということで早速触ってみました。
http://store.steampowered.com/app/363890/
（日本語版は12月。何故か英語版の方が発売が早い・・・）

ちなみに、ツクール触るのは2003ぶりです。

# RPGツクールMVって？

RPGツクールの最新作。
今回はWindowsの他に、Mac, iOS, Androidそしてhtml5にも出力できちゃいます。
https://tkool.jp/mv/

あと、[オンラインゲームとか作れちゃうみたいです。](http://qiita.com/kyubuns/items/4108221a8b8245e46d27)

# GitHub Pagesって？
GitHubには、GitHub Pagesというリポジトリをそのままwebサイトとして公開できる便利な機能があります。
https://pages.github.com/

そして、RPGツクールMVのプロジェクトは、
「アップロードすればwebサイト上でそのまま遊べる」形式になっています。
（プロジェクトを作ると、普通にindex.htmlとか入ってる。）
つまり、GitHubでプロジェクト管理しつつ、そのまま公開までできちゃうんです！
一石二鳥。

# やってみる
## 1: まずはリポジトリを作る

RPGツクールでプロジェクトを作って、そこで```git init .```。

マニュアルに「* The Game.rpgproject and save folder are not needed to run the game」と書いてあったので、そこは.gitignoreで弾く設定をいれました。

後は普通にgithubにpush。
これでリポジトリは出来ました。

https://github.com/kyubuns/rpg_maker_test

これだけじゃまだwebページは出来ていません。

## 2: GitHubPagesを利用する

gh-pagesブランチの内容が勝手にwebサイトとして公開されます。
なので
```git push origin master:gh-pages```
とでもしてやればOKです。
https://github.com/kyubuns/rpg_maker_test/tree/gh-pages

## 3: これだけ！！

他の設定は特に必要ありません！
```http://(アカウント名).github.io/(リポジトリ名)/```
でアクセスできます！

http://kyubuns.github.io/rpg_maker_test/

# あとがき

RPGツクールMV触り始めて2日目ですが懐かしさと新しさがあって超楽しい！！！
色々スクリプトの情報交換とかしたいので、RPGツクールユーザーの方フォローお待ちしてます。
(リプライ貰えるとフォロー返しやすいです！)
http://twitter.com/kyubuns

