##地址解析

###简介

地址解析`searchWithGeoCodeSearchOption:revGeoOption`接口提供由地址描述到所述位置坐标的转换功能。更详细的接口描述可以查阅[官网](https://lbs.qq.com/webservice_v1/guide-geocoder.html)。这里对 SDK 提供的地址解析接口做简要描述。

<br>

###地址解析接口参数
<hr>
设置地址解析检索参数 QMSGeoCodeSearchOption，其中 address 为必填字段：

| 属性 | 说明
| --- | ---
| NSString *address | 必填，用于做地理编码的地址；<br>如：address=北京市海淀区彩和坊路海淀西大街74号
| NSString *region | 指定地址所属城市；<br>如：region=北京

示例如下：

```objC
QMSGeoCodeSearchOption *geoOption = [[QMSGeoCodeSearchOption alloc] init];
[geoOption setAddress:@"北京市海淀区彩和坊路海淀西大街74号"];
[geoOption setRegion:@"北京"];
```

<br>

###发起地址解析检索
<hr>
调用QMSSearcherAPI中的`searchWithGeoCodeSearchOption:`发起地址解析检索

```objC
[self.mySearcher searchWithGeoCodeSearchOption:revGeoOption];
```

<br>

###在回调中处理搜索数据
当检索成功后，会调用到`searchWithGeoCodeSearchOption: didReceiveResult:`回调函数，通过解析 QMSGeoCodeSearchResult 数据把所需的结果绘制到地图上。

```objC
- (void)searchWithGeoCodeSearchOption:(QMSGeoCodeSearchOption *)geoCodeSearchOption didReceiveResult:(QMSGeoCodeSearchResult *)geoCodeSearchResult
{ 
    NSLog(@"geoCodeResult: %@", geoCodeSearchResult);
}
```

####QMSReverseGeoCodeSearchResult类属性说明：

| 属性 | 说明
| --- | ---
| CLLocationCoordinate2D location | 解析到的坐标(经纬度)
| QMSAddressComponent *address_components | 解析后的地址部件
| QMSGeoCodeAdInfo *ad_info | 行政区划信息

表格中的 QMSAddressComponent 和 QMSGeoCodeAdInfo 详细类别请参考QMSSearchResult.h文件说明

<br>

###效果示例
<hr>
在北京地区检索“北京市海淀区彩和坊路海淀西大街74号”，示例如下：

```objC
QMSGeoCodeSearchOption *geoOption = [[QMSGeoCodeSearchOption alloc] init];
[geoOption setAddress:@"北京市海淀区彩和坊路海淀西大街74号"];
[geoOption setRegion:@"北京"];
[self.mySearcher searchWithGeoCodeSearchOption:revGeoOption];
```

根据地址检索出的结果生成对应的POI点：

<img src="../images/search-geo1.png" width="60%">

POI信息详情如下：

<img src="../images/search-geo2.png" width="60%">

<br>

###错误信息回调
<hr>
当检索失败时，回调函数`searchWithSearchOption: didFailWithError:`会返回对应的错误信息

```objC
- (void)searchWithSearchOption:(QMSSearchOption *)searchOption didFailWithError:(NSError *)error
{
    NSLog(@"%@",error);
}
```

更详细设置请参考demo