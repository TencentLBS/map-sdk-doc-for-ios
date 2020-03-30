##个性化图层

图展示所用的瓦片并放到合适的位置，提升如景区、园区在地图中的展现效果。平台提供服务进行瓦片制作、图层存储、权限管理等，对于调用自定义瓦片图层（QCustomLayer）接口的使用成本大幅降低。

#### 第一步 创建图层
<hr>
通过SDK使用个性化图层能力前，需要用户通过位置服务官网的编辑平台创建图层并生成图层id。网址：[https://lbs.qq.com/dev/console/customLayer/guide](https://lbs.qq.com/dev/console/customLayer/guide)

<br>

####第二步 添加图层
<hr>

#####1. 工程配置
腾讯地图 iOS SDK 接入示例请参考：[https://github.com/TencentLBS/TencentMapDemo_iOS](https://github.com/TencentLBS/TencentMapDemo_iOS ) 中的 README 文档。

地图其他功能使用请一同参考上述链接。

####2. 申请用户key
1. 访问 [控制台](https://lbs.qq.com/dev/console/key/add) 申请密钥。
2. 填写应用名称，描述，验证码，阅读并同意使用条款。
3. 申请完成后，通过密钥设置勾选地图SDK选项，获得使用权限。

####3. 在项目中添加key
在使用地图SDK时，需要对应用做Key机制验证，如果地图不添加key，地图将不能运行，控制台会显示没有key的错误日志。

在AppDelegate.m中配置key方法如下：

```objC
#import <QMapKit/QMapKit.h>


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Override point for customization after application launch.
    [QMapServices sharedServices].APIKey = @"您的key";
    return YES;
}
```
  
####4. 创建自定义图层类
使用个性化图层功能时，需初始化自定义图层的类 （QCustomLayer），它包含一个图层的唯一标识 （layerID），在个性化图层编辑平台成功上传图层时得到的其标识字符串。初始化 QCustomLayer 后，把得到的图层标识进行赋值，示例如下：

```objC
QCustomLayer *testlayer = [[QCustomLayer alloc] init];
testlayer.layerID = @"填入图层标识";
```
          
####5. 添加自定义图层
当 QCustomLayer 类初始化完，并进行图层标识赋值后，就可以把自定义图层添加到地图上，示例如下：

```objC 
// 将自定义图层添加到地图上
[self.mapView addCustomLayer:testlayer];
```
          
####6. 移除自定义图层
当自定义图层不需要显示时，可以把对应的自定义图层在地图上移除，示例如下：

```objC
[self.mapView removeCustomLayer:testlayer];
```