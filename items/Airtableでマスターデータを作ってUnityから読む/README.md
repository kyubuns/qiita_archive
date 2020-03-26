## Airtable

ゲームのマスターデータ、何のツールで管理してますか？
Excel? GoogleSpreadsheet?
Airtableっていうめっちゃ便利なサービスがあります。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">GoogleSpreadsheetをマスターデータに使ってる！とか、DBみたいに使ってるならAirtableめっちゃオススメ<br>制約が必ず列単位でつくから「この行だけ設定違う💢」ってなんないし<br>同じデータから別の見え方View作ったり出来るし<br>本当に求めていたものって感じ<a href="https://t.co/JQMhyOQT5g">https://t.co/JQMhyOQT5g</a> <a href="https://t.co/KkFd9UHzy7">pic.twitter.com/KkFd9UHzy7</a></p>&mdash; きゅぶんず (@kyubuns) <a href="https://twitter.com/kyubuns/status/1148042472934658048?ref_src=twsrc%5Etfw">July 8, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">他テーブルの行の参照つっこんだり、画像突っ込んだりも出来る <a href="https://t.co/uJ7aVoUJ1s">pic.twitter.com/uJ7aVoUJ1s</a></p>&mdash; きゅぶんず (@kyubuns) <a href="https://twitter.com/kyubuns/status/1148111704644997122?ref_src=twsrc%5Etfw">July 8, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## マスターデータを作る

マスターデータを作ります。
「この列は数字だけ！」とかの制約がかけれたり、Filterがかけれたりしてめっちゃ便利！っていうかもはや普通にDBです。

<img width="350" alt="Screen Shot 2019-07-08 at 15.50.24.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/6459/8f17f18a-9bda-3a95-6cd8-6dde40c628e8.png">

## Unityから読む

めっちゃ分かりやすいAPIのドキュメントがあります。
ApiKeyとBase(ドキュメントのID的なもの)もここから見れます。
https://airtable.com/api

[GitHub : kyubuns/airtable.net](https://github.com/kyubuns/airtable.net)
↑公式からForkして[若干改造](https://github.com/ngocnicholas/airtable.net/pull/16)入れてあります。
AirtableApiClientディレクトリ以下をUnityに突っ込んで、Json.netもどこかから調達してきてください。
そしたら後はこれだけです。

```csharp
using (var table = new AirtableBase(ApiKey, Base))
{
    string offset = null;
    var response = await table.ListRecords<Player>("Player");
    foreach (var r in response.Records)
    {
        var player = r.Fields;
        Debug.Log($"{player.Lv} - {player.HP}");
    }
}

public class Player
{
    public int Lv;
    public int HP;
}
```

1回の呼び出しに付き100行までしか取ってこれないので、1テーブルが100行以上になりえるときはこんな感じ。

```csharp
string offset = null;
do
{
    var response = await table.ListRecords<Player>("Player");
    if (!response.Success) break;
    foreach (var r in response.Records)
    {
        var player = r.Fields;
        Debug.Log($"{player.Lv} - {player.HP}");
    }
} while (offset != null);
```

## 注意点

> The API is limited to 5 requests per second.

1秒間に5リクエストで制限かかるので、ユーザーが直接読むのには向いてなさそうです。
この制約がキツい場合はお問い合わせくださいって書いてあるので、有料プランだときっと上がるんでしょう。きっと。
