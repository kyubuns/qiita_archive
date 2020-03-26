# 前提

- iOSの話をします。きっとAndroidも似たような感じで出来ます。
- 説明に使っているプロジェクトはこちら。
    - [GitHub:kyubuns/StreamingAssetsDemo](https://github.com/kyubuns/StreamingAssetsDemo)

# 実現出来るようになること

このUIを
<img width="200" alt="Screen Shot 2019-08-03 at 18.15.50.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/6459/e12b3697-5eff-b5d5-58d6-b2052067d289.png">

こんな感じに編集したら
<img width="200" alt="Screen Shot 2019-08-03 at 18.18.06.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/6459/42c6b660-882d-d5f5-f348-67aa0d146d8f.png">

1分以内(※体感には個人差があります)に手元のiPhoneで新しいUIを確認できる！！

<img width="200" alt="IMG_D27513BE54C9-1.jpeg" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/6459/a1d49a5e-598f-879e-966b-20df06a46bed.jpeg">

# 考えてみる

ボタンの位置をちょっと変更しただけなのに、ビルドを10分待たないと確認できない・・・っていうのは時間の無駄です。
UnityのiOSビルドは頑張っても([Qiita:UnityプロジェクトのiOSビルドをなるべく早くする](https://qiita.com/kyubuns/items/2d9d28e60823a7818ca5))そこそこの時間がかかるので、
ビルドしないでUIだけ差し替える方法を考えてみます。

## 案1 : UIをAssetBundleにしてWebで配信する

まあみんなやってるだろうなってやつです。
リリースバージョンでもAssetBundleはWebで配信する予定なら開発中もこれやってるところが多いと思います。

## 案2 : ipaの中身を無理矢理いじってAssetBundleを置き換える

今回ご紹介したいのはこちら。
AssetBundleをWebで配信する予定は無いし、わざわざ入れるのもなーって方向け。
ビルド済みのipaのStreamingAssets内に入っているAssetBundleを差し替え、再び署名すれば最速で配信出来る！という荒業です。

```bash
unzip original.ipa
cp Assets/StreamingAssets/AssetBundles/* Payload/*.app/Data/Raw/AssetBundles/
codesign -d --entitlements :- Payload/*.app > entitlements.plist
codesign --force --sign 'iPhone Distribution: **********' --entitlements entitlements.plist --timestamp=none Payload/*.app
zip -r new.zip Payload
mv new.zip new.ipa
```

普通にビルドすると10分かかるプロジェクトでも、この方法ならたった5秒で新しいipaが出来上がります。
ぜひご活用ください。

# あとがき

ゲーム開発効率化ネタが好物なので、こういう話が好きな人は是非twitter等でご連絡ください。
[@kyubuns](https://twitter.com/kyubuns)
