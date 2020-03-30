



## 使用开发密钥

### 在项目中添加key

在使用地图SDK时，需要对应用做Key机制验证，如果地图不添加key，地图将显示**鉴权失败,请检查你的Key**的错误信息，控制台也同时会显示**key 鉴权失败:xxx**的错误日志和原因。

key的使用方法如下：

```objC
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Override point for customization after application launch.
    [QMapServices sharedServices].APIKey = @"您的key";
    return YES;
}
```



地图SDK的工程配置完成。 后面的文档将介绍如何在工程中使用地图SDK显示地图。

(请注意，地图作为一个 UI 控件，请保证对地图所有接口的使用都在**主线程调用** ！)





