1,2年前に、色んなIAPプラグインを漁って「このプラグインはコレが出来ない・・・」「こっちはこれが・・・」みたいな研究をした苦い思い出があるので
UnityIAPについては、ここで軽くまとめておきます。

[Unity - Manual: Unity IAP](http://docs.unity3d.com/Manual/UnityIAP.html)

* 信頼性
 * Unityが保守してくれるという意味で、今後も無くならないという安心感。
 * まだリリースされたばっかりだからバグはあるかも という不安。
* Unityへのインポート
 * 専用UIがあるから超楽。
* iOS/Android両対応のコスト
 * iOSの時はこれをして、Androidの時はこれをして・・・みたいなのはほぼ無し。
 * ほぼほぼ共通コードで行ける。
 * 今のところ、iOS専用で書いたのはRestore処理、Android専用で書いたのはSetPublicKey。OS依存の処理はこのたった2行。
* Editor上でデバッグ出来る？
 * 出来る。
 * Editor上では全部$0.01扱いになっていて、購入処理を走らせると専用のUIで「購入しますか？はい/いいえ」みたいなのが出るので、エディター上で購入した時のフロー及びOSレイヤーで購入をやっぱりやめた時のフローそれぞれ確認できる。
* サーバーサイドでレシート検証
 * 出来る。
 * 詳細はこちら http://docs.unity3d.com/Manual/UnityIAPProcessingPurchases.html
* レシートの取得
 * 出来る。
* ローカライズ済みの価格表示の取得（Storeによって「$0.99」になったり「¥120」になったりするstring）
 * 出来る。
* Ask To Buy(parental control対応)
 * 出来る
 * http://docs.unity3d.com/Manual/UnityIAPiOSMAS.html

結論: すごい。
エディター上で購入フローのデバッグできるの感動した。
