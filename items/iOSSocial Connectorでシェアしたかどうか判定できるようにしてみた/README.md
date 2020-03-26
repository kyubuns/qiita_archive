anchan828さんのsocial-connectorをforkしたものです。
https://github.com/kyubuns/social-connector

iOS限定の対応になるので、PR上げるのはAndroid対応してからにします。

```Sample.cs
SocialConnector.Callback += (completed, activityType, stringActivityType) => {
    Debug.LogFormat("SocialConnector.Callback activityType={0}, completed={1}", activityType, completed);
};
```

https://github.com/kyubuns/social-connector/blob/master/Assets/SocialConnector/Sample/Sample.cs#L15

こんなかんじに、シェアしたかどうかを取得できます。
ActivityTypeは、アプリが拡張して増えていく分に関してはどうしようもないのでstringで返してます。
MailとかTwitterとかはenumで帰ってきます。

ちなみに、「シェアする」って押したかどうか、がとれるだけで、プラットフォーム側でのエラーは検知できないです。
例えば、twitterで同じ文言を2回ツイートしようとしてエラーになって弾かれる、とか。
