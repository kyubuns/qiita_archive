開発中のアプリって「あれ？これ最新バージョンだっけ」とか「そのバグ治したはずなんだけど、まだビルドされてないのかな・・・」みたいな現象が起こりえます。
もともとアプリ内では開発中のどのバージョンか（何がマージされたところまでビルドされているか）を確認できるようにしていたのですが、こちらの記事 http://qiita.com/Tueno@github/items/4c2a24e1f18a583a69b7 を読んで、ああ、これは良い！って思ったので何をしたか書いておきます。

# 先に言い訳

本当はUnity内(C#)で解決したかったんですけど、Editor上で画像に文字いれたり文字列操作したりするのがめんどくさかったのでbashでやっちゃいました
良い方法あったら教えてください

# 解決策: マージコミットの数を数えてバージョンにしてしまう

```bash
VERSION=`git log --merges --pretty=oneline | wc -l | xargs`
sed -i -e "s/Dev/dev_${VERSION}/g" Version.cs
convert icon.png -fill black -stroke white -strokewidth 2 -pointsize 110 -gravity south -annotate +0+10 "dev_${VERSION}" icon.png

# Unityプロジェクトビルド
```

アプリ内でも表示出来るようにsedで無理矢理ソースコードを置換し、アイコンにもimagemagickで無理矢理書いてます。

# まとめ

かなり力技ですが、ちゃんと数字も増えていくし別のジョブでも数字がデカイほうが最新だと分かりやすい。
後はこのVERSIONを使ってslackとかに変更内容を通知してやれば良い感じに運用できるかな〜と思ってます。
他にも「こういう風にしてるよ！」みたいなのがあったらぜひ聞いてみたいのでコメントか[twitter](http://twitter.com/kyubuns)までお気軽にどうぞ！

# ダメだったやつ


## ダメだったやつ1: Jenkinsのビルド番号を入れる

```bash
VERSION=${BUILD_NUMBER}
# 2行目以降は同じ
```

### 問題点

* 別のプラットフォームのビルドはJenkinsジョブが違うので、ビルド番号がずれることがあり得る
  * 1つのジョブをトリガーにして、そのビルド番号を使おうと思ったけれど、片方のプラットフォームだけビルドしたいことが多々あったのでやめた。

## ダメだったやつ2: 最後にマージされたPRの番号を入れる

```bash
VERSION=`git log --merges HEAD...HEAD~20 --oneline | awk '{print substr($0,index($0,"#")+1)}' | awk '{sub(" from.*","");print $0;}' | grep '^[0-9]*$' | sort -n | tail -1`
# 2行目以降は同じ
```

### 問題点

* PRは順番にマージされていくわけではないので、ビルドのたびに数字が大きくなるとは限らない
