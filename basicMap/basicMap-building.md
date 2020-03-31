## 显示建筑物

腾讯 iOS 3D 地图 SDK支持建筑物是否显示的开关配置，也支持是否显示3D立体效果的配置。当显示3D建筑物时，地图有一定的倒伏角效果会更好。

接口介绍：

| 功能                         | 实现接口                                     |
| ---------------------------- | -------------------------------------------- |
| 开启/关闭建筑物 (默认为YES)  | @property (nonatomic) BOOL showsBuildings;   |
| 显示/关闭3D效果（默认为YES） | @property (nonatomic) BOOL shows3DBuildings; |

示例代码：

```objC
// 显示建筑物
self.mapView.showsBuildings = YES;
// 显示建筑物的3D效果
self.mapView.shows3DBuildings = YES;
```

效果图：(左边为3D效果，右边为非3D效果)

<img src="../images/building1.png" width="300" ><img src="../images/building2.png" width="300" >



