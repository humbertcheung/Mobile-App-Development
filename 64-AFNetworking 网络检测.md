# AFNetworking 网络检测

### 网络检测

在这个手机应用横飞的年代，应用程序的用户体验已越来越被人们所重视了。对于流量而言，虽然三大运营商已经推出了无限流量的套餐，但是对于网络状况的检测对于用户来说仍然显得尤为重要。因此我们在开发APP的时候需要检测用户设备的网络状态，实时告知用户当前所使用的网络，避免出现用户手机流量超出而未被告知，从而提高程序的用户体验。
AFNetworking框架，我们除了可以使用它来完成网络请求，同时，它也为我们提供来网络状态检测的功能。

### 检测使用步骤

##### 1、创建监听管理对象

需要注意的是监听管理对象是单例对象，所以我们需要通过share()方法来获取该单例对象。

```swift
// 获取AFN的网络检测单例
let reachabilityManager = AFNetworkReachabilityManager.shared()
```

2、打开检测


```swift
// 打开网络检测
reachabilityManager.startMonitoring()
```

3、检测网络并在状态变化时执行闭包


```swift

reachabilityManager.setReachabilityStatusChange { (status) in

    switch status {
    case AFNetworkReachabilityStatus.unknown:
        print("未知网络")
    case AFNetworkReachabilityStatus.notReachable:
        print("网络无连接")
    case AFNetworkReachabilityStatus.reachableViaWWAN:
        print("3G/4G网络")
    case AFNetworkReachabilityStatus.reachableViaWiFi:
        print("WiFi")
    default :
        print("网络状态异常")
    }
}
```

###### AFNetworkReachabilityStatus

网络连接状态如下：

```swift
typedef NS_ENUM(NSInteger, AFNetworkReachabilityStatus) {
    AFNetworkReachabilityStatusUnknown          = -1, //未知
    AFNetworkReachabilityStatusNotReachable     = 0,  //无连接
    AFNetworkReachabilityStatusReachableViaWWAN = 1,  //3G
    AFNetworkReachabilityStatusReachableViaWiFi = 2,  //WIFI
};
```


