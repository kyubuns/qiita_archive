## まえがき

需要もあんまり無さそうなので手短にまとめます。
質問がありましたら、[twitter](http://twitter.com/kyubuns)、コメントなどでどうぞ。

## やりたいこと

app_name://hogefugapiyo

というリンクをどこかにしこんでおいて、これを踏んだら、app_nameというアプリを起動し、さらにhogefugapiyoという情報を渡す。

## 実装
### URL Schemeを設定する

URL Schemeを設定する方法は、前回紹介した通り。

http://qiita.com/kyubuns/items/6cbc982a3d96e8f4c5ce

### 情報を受け取る

以下のObj-Cのコードを、Plugins/iOSにつっこむか、[なんやかんや](http://qiita.com/kyubuns/items/28dd316ef2415fc9a48d)してコードを突っ込んでください。

```lang:ExpandAppController.mm
#if ! __has_feature(objc_arc)
#error This file must be compiled with ARC. Either turn on ARC for the project or use -fobjc-arc flag
#endif

#import "UnityAppController.h"

extern "C"
{
	void UnitySendMessage(const char* obj, const char* method, const char* msg);
}

char* MakeStringCopy(const char* string)
{
	if (string == NULL) return NULL;
	char* res = (char*)malloc(strlen(string) + 1);
	strcpy(res, string);
	return res;
}

@interface ExpandAppController : UnityAppController
@end

@implementation ExpandAppController
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
	NSString* message = [url absoluteString];
	UnitySendMessage("Receiver", "TriggerOpenURL", MakeStringCopy([message UTF8String]));
	return YES;
}
@end

IMPL_APP_CONTROLLER_SUBCLASS(ExpandAppController)
```


頭からの3行は、ARCを利用しない場合は消してください。
ARCを利用する方法はこちらで紹介しています。
http://qiita.com/kyubuns/items/24d33044e77b9fa614b3#2-6

これで、ReceiverというGameObjectのTriggerOpenURLに「app_name://hogefugapiyo」って文字列が渡されます。
