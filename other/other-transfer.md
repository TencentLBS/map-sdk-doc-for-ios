## 坐标转换

iOS地图SDK中共支持两种坐标数据：

+ CLLocationCoordinate2D：经纬度坐标
+ QMapPoint：平面投影坐标

在 QGeometry 工具类中提供了这两种坐标相互转换的方法：

```objC
/**
 *  @brief 经纬度坐标转平面投影坐标
 *
 *  @param coordinate 要转化的经纬度坐标
 *
 *  @return 平面投影坐标
 */
extern QMapPoint QMapPointForCoordinate(CLLocationCoordinate2D coordinate);
    
/**
 *  @brief 平面投影坐标转经纬度坐标
 *
 *  @param mapPoint 要转化的平面投影坐标
 *
 *  @return 经纬度坐标
 */
extern CLLocationCoordinate2D QCoordinateForMapPoint(QMapPoint mapPoint);
```