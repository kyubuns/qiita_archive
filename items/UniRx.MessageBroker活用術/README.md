UniRx.MessageBrokerを日々 ~~悪用~~ 活用してるので、便利かも！って作ったクラスを公開しておきます。

# TypedMessageBroker

MessageBrokerのPublish/Subscribeに型で制限をつける。

## 用途

- Brokerがたくさんあるので、送り先を間違えるのを防ぐ。

## サンプル

```csharp
public void Start()
{
    var broker1 = new TypedMessageBroker<IMessageType>();

    broker1.Publish(new Message1());
    // broker1.Publish(new Message2()); // IMessageTypeじゃないのでビルドエラー
}

public interface IMessageType {}
public class Message1 : IMessageType {}
public class Message2 {}
```

## コード

https://github.com/kyubuns/UniRxSandbox/blob/master/Assets/TypedMessageBroker/TypedMessageBroker.cs

# QueueAsyncMessageBroker

PublishしたMessageの処理が全て終わるまで、次のMessageのPublishを待つ。

## 用途

- アニメーション処理を全部Publishしておいたら良い感じに再生される

## サンプル

```csharp
var broker = new QueueAsyncMessageBroker();

broker.Subscribe<Message>(x => Observable.Timer(TimeSpan.FromSeconds(x.Value)).AsUnitObservable()).AddTo(this);
broker.Subscribe<Message>(x =>
{
    Debug.Log($"{Time.time} Receive Message {x.Value}");
    return Observable.ReturnUnit();
}).AddTo(this);

broker.Publish(new Message(3));
broker.Publish(new Message(1));
broker.Publish(new Message(2));
```

```
0 Receive Message 3
3 Receive Message 1
4 Receive Message 2
```

## コード

https://github.com/kyubuns/UniRxSandbox/blob/master/Assets/QueueMessageBroker/QueueAsyncMessageBroker.cs

## 欠点

- Subscribeの中で例外吐いた時にcall stackが死んでる
