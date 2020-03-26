# まえがき

これはPhoton以外でオンラインゲームを作ったことがある人が勘違いしやすい点かなと思う。
自分がアホなだけかもしれない。
なので、タイトルを読んで「当たり前じゃないの？？」と思った方は、逆に混乱させるだけだと思うのでここで戻るボタンを押してください。

# 自分が勘違いしていた点

例えば、同じルーム内にA,B,C,Dという4人のプレイヤーがいたとする。
この時、ルーム内に1つだけ存在するアイテムに対して「Aがアイテムを取得した」というRPCを呼ぶのと「Bがアイテムを取得した」というRPCを呼ぶことが同時に起こりえる。

具体的にコードに起こすと、2クライアントが同時に以下のコードを呼ぶ場合である。

```csharp
photonView.RPC("GetItem", PhotonTargets.All);

[PunRPC]
public void GetItem(PhotonMessageInfo info)
{
  if(isActive)
  {
    Debug.Log(info.Sender + "がアイテムを取ったよ！");
    isActive = false;
  }
}
```

この時、Aのクライアントでは「Aがアイテムを取ったよ！」と表示され、Bのクライアントでは「Bがアイテムを取ったよ！」と表示される。
さらに具合の悪いことに、C,Dのクライアントでは「Aがアイテムを取ったよ！」「Bがアイテムを取ったよ！」のどちらが表示されるのかは不定である。
CとDの結果は同じかもしれないし、違うかもしれない。
本当にクライアント同士が直接通信しているオンラインゲームであれば、このようなケースを考慮しなければならない。

# 冷静に考えてみる

Photonは、クライアント同士がメッセージのやりとりをしているわけではなく、あくまでクライアントとPhotonServer間の通信しかしていない。
つまり、「AがPhotonTargets.Allに送信する」というのは、「AがPhotonServerに送信する」「PhotonServerがB,C,Dに送信する」ということ。
何が言いたいかというと、みんな同じPhotonServerというところを経由しているんだから全クライアントのRPCの受信順は保証されるはずである。

上に書いたコードは間違いで、正しくはこう。

```csharp
photonView.RPC("GetItem", PhotonTargets.AllViaServer); // <- Allを使うとローカルだけ先に実行されてしまう

[PunRPC]
public void GetItem(PhotonMessageInfo info)
{
  if(isActive)
  {
    Debug.Log(info.Sender + "がアイテムを取ったよ！");
    isActive = false;
  }
}
```

このコードは、全てのクライアントで同じ結果になる。

# あとがき

Photonは使えば使うほど「中の人、よくわかってるな〜」と感心させられる実装になっているので、流行ってるだけあるなと思いました（小並感）

# 参考

> Example: In a racing game you could send the RPC "finished" as AllViaServer. The first "finished" RPC call will tell you who won. The following "finished" calls will tell you who's on the ranks.
http://doc.photonengine.com/ja/pun/current/tutorials/rpcsandraiseevent
