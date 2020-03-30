## 距离计算

iOS地图SDK中提供了 QGeometry 工具类，该类中包含了地图中如坐标、位置等数据定义和工具方法，有关距离计算的方法如下：

```objC
/**
 *  @brief 返回两个 QMapPoint 之间的距离, 单位:米
 *
 *  @param a 点 a
 *  @param b 点 b
 *
 *  @return 距离值
 */
extern CLLocationDistance QMetersBetweenMapPoints(QMapPoint a, QMapPoint b);
    
/**
 *  @brief 返回两个经纬度之间的距离, 单位:米
 *
 *  @param a 点 a
 *  @param b 点 b
 *
 *  @return 距离值
 */
extern CLLocationDistance QMetersBetweenCoordinates(CLLocationCoordinate2D a, CLLocationCoordinate2D b);
```

以西二旗地铁站和腾讯北京总部为例计算，示例代码如下：

```objC
// 西二旗地铁站
CLLocationCoordinate2D coord1 = CLLocationCoordinate2DMake(40.053034, 116.306295);
// 腾讯北京总部大楼
CLLocationCoordinate2D coord2 = CLLocationCoordinate2DMake(40.040219, 116.273348);
CLLocationDistance distance = QMetersBetweenCoordinates(coord1, coord2);
    
// 两地距离：3149.288199
NSLog(@"%f", distance);
```