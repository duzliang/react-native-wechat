# Build Setup for iOS

Add the following libraries to your "Link Binary with Libraries" in Targets > Build Phases :

- [x] `SystemConfiguration.framework`
- [x] `CoreTelephony.framework`
- [x] `libsqlite3.0`
- [x] `libc++`
- [x] `libz`

Add `pod 'WechatOpenSDK'` to `Podfile` and execute
```shell
pod install
```

Add "URL Schema" as your app id for "URL type" in Targets > info, See 
the following screenshot for the view on your XCode:

![Set URL Schema in XCode](https://i.loli.net/2019/08/31/yUD2F5MrPKjngo3.jpg)

On iOS 9+, add `wechat` and `weixin` into `LSApplicationQueriesSchemes` in 
`Targets` > `info` > `Custom iOS Target Properties`. Or edit `Info.plist` 
then add:

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>weixin</string>
  <string>wechat</string>
  <string>weixinULAPI</string>
</array>
```

若需要处理universal links，则在`AppDelegate.m`中添加如下方法，并在`AppDelegate.h`中引入`WXApi.h`文件和添加协议`WXApiDelegate`

Step1:

```
#import "WXApi.h"

@interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate, WXApiDelegate>
```

Step2:

```
// AppDelegate.m didFinishLaunchingWithOptions方法中添加如下方法
// 处理Universal Links跳转进来的请求
- (BOOL)application:(UIApplication *)application continueUserActivity:(nonnull NSUserActivity *)userActivity restorationHandler:(nonnull void (^)(NSArray * _Nullable))restorationHandler
{
    NSLog(@"%@", userActivity.webpageURL);
    // 触发回调方法
	  [RCTLinkingManager application:application continueUserActivity:userActivity restorationHandler:restorationHandler];
    return [WXApi handleOpenUniversalLink:userActivity delegate:self];
}
```



Then copy the following in `AppDelegate.m`:

```objc
#import <React/RCTLinkingManager.h>

// ios 8.x or older
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
  return [RCTLinkingManager application:application openURL:url
                            sourceApplication:sourceApplication annotation:annotation];
}

// ios 9.0+
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
            options:(NSDictionary<NSString*, id> *)options
{
  return [RCTLinkingManager application:application openURL:url options:options];
}
```

