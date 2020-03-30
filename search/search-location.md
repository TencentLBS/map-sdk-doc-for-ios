##地点搜索

###简介
地点搜索 `searchWithPoiSearchOption:(QMSPoiSearchOption *)poiSearchOption;` 接口，封装了腾讯地图 WebService API 的地点搜索功能，更详细的接口描述可以查阅[官网](https://lbs.qq.com/webservice_v1/guide-search.html)。这里对 SDK 提供的地点搜索接口做简要描述。

地点搜索根据搜索参数的不同，主要分为以下几个功能：

1. 搜索参数概览
2. 发起搜索和结果回调
3. 指定地区搜索
4. 周边搜索
5. 矩形搜索

<br>

###搜索参数概览
<hr>
####参数概览
QMSPoiSearchOption类型为地点搜索的请求参数，该类型包含如下属性：

| 属性 | 说明
| --- | ---
| NSString *keyword | **必填**<br>POI搜索关键字，用于全文检索字段<br>例如：keyword=酒店
| NSString *boundary | **必填**<br>搜索地理范围, 语法请参考[官网](http://lbs.qq.com/webservice_v1/guide-search.html#boundary_detail )介绍
| NSString *filter | 筛选条件，语法请参考[官网](https://lbs.qq.com/webservice_v1/guide-search.html#filter_detail)
| NSUInteger page_size | 请求数据每页条目数，最大限制为20条，默认10条。
| NSUInteger page_index | 第x页，默认第1页
| NSString *orderby | 排序，目前仅周边搜索（boundary=nearby）

####构建关键字搜索参数
1. 设置关键字搜索参数 QMSPoiSearchOption，keyword为必填字段

	```objC
	QMSPoiSearchOption poiSearchOption = [[QMSPoiSearchOption alloc] init];
	
	[poiSearchOption setKeyword:@"餐厅"];
	// 或者
	poiSearchOption.keyword = @"餐厅";
	```
2. page\_size 和 page\_index 参数
	+ page\_size：每页条目数，最大限制为20条，默认10条。
	+ page\_index：为搜索结果的页数索引，第x页，默认为第1页。

	```objC
	// 每页条目数
	[poiSearchOption setPage_size:5];
	或者
	poiSearchOption.page_size = 10;
	
	// 页数索引
	[poiSearchOption setPage_index:3];
	或者
	self.poiSearchOption.page_index = 3;
	```
3. filter参数

	filter为筛选条件，用户通过设置特定的条件对搜索结果进行过滤，最多支持5个分类词。

	搜索指定分类：

	例如传入"category=公交站"则最终会被组装为filter=category=公交站，筛选出包含“公交站”的结果分类

	```objC
	[poiSearchOption setFilter:@"category=公交站"];
	```

	搜索多个分类：

	举例："category=大学,中学"（以英文逗号分隔筛选条件）筛选出包含“大学,中学”的结果分类

	```objC
	[poiSearchOption setFilter:@"category=大学,中学"];
	```

	用户还可以传入NSString类型数组通过 `setFilterByCategories:` 接口构建filter，最多支持5个分类词。

	```objC
	NSArray <NSString *> *filterArray = [NSArray arrayWithObjects:@"烧烤", @"日料", @"中餐", nil];
	[poiSearchOption setFilterByCategories:filterArray];
	```

	注：腾讯地图POI分类关键词参考：[http://lbs.qq.com/webservice_v1/guide-appendix.html](http://lbs.qq.com/webservice_v1/guide-appendix.html)

4. orderby参数

	orderby为排序方式，目前仅支持周边搜索（boundary=nearby)。支持按距离由近到远排序，默认取值取值：_distance，以到boundary的中心点距离排序；当orderby为空时，以POI权重排序。
	
<br>

###发起搜索和结果回调
<hr>
#### 1. 发起POI检索：

调用QMSSearcherAPI中的`searchWithPoiSearchOption:`发起指定区域检索

```objC
[self.mySearcher searchWithPoiSearchOption:poiSearchOption];
```

#### 2. 回调中出解析数据
当检索成功后，会调用到`searchWithPoiSearchOption: didReceiveResult:`回调函数:

```objC
- (void)searchWithPoiSearchOption:(QMSPoiSearchOption *)poiSearchOption didReceiveResult:(QMSPoiSearchResult *)poiSearchResult
{
  // 获取结果中的第一个POI数据 
  QMSPoiData *poiData = [poiSearchResult.dataArray objectAtIndex:0];
	
  NSLog(@"result is: %@", self.poiSearchResult);
	
  // 详细POI数据解析，请参考demo
}
```

QMSPoiSearchResult参数说明：

| 属性 | 说明
| --- | ---
| NSUInteger count | 本次搜索结果总数
| NSArray *dataArray | 搜索结果POI数组，每项为一个POI(QMSPoiData)对象	
QMSPoiData参数说明：

| 属性 | 说明
| --- | ---
| NSString *id_ | POI唯一标识
| NSString *title | poi名称
| NSString *address | 地址
| NSString *tel | 电话
| NSString *category | POI分类
| QMSPoiType type | POI类型，值说明：<br>0：普通POI<br>1：公交车站<br>2：地铁站<br>3：公交线路<br>4：行政区划
| CLLocationCoordinate2D location | 坐标(经纬度)
| NSArray *boundary | 轮廓，坐标数组，面积较大的POI会有，如住宅小区。数组里为CLLocationCoordinate2D类型数据。<br>**非必有字段**

#### 3. 错误信息回调
当检索失败时，回调函数`searchWithSearchOption: didFailWithError:`会返回对应的错误信息：

```objC
- (void)searchWithSearchOption:(QMSSearchOption *)searchOption didFailWithError:(NSError *)error
{
    NSLog(@"%@",error);
}
```

更详细设置请参考demo

<br>

###指定地区搜索
<hr>
QMSPoiSearchOption搜索参数中的boundary属性为指定搜索的地区，该参数可以通过字符串方式构建，也可以通过QMSPoiSearchOption提供的`setBoundaryByRegionWithCityName:`方法来构建。

#### 1. 以字符串形式配置请求参数：
参数格式：`boundary=region(city_name, [auto_extend], [lat,lng])`

参数说明：

| 参数 | 说明
| --- | ---
| city_name | 检索区域名称，城市名字，如：`city_name=@"北京市"`。
| auto_extend | 可选参数，取值为0和1。<br>取值1：默认值，若当前城市搜索无结果，则自动扩大范围；​<br>取值0：仅在当前城市搜索。
| lat,lng | 可选参数。<br>当用户使用泛关键词搜索时（如酒店、超市），这类搜索多为了查找附近，使用此参数，搜索结果以此坐标为中心，返回就近地点，体验更优。

示例如下：

```objC
// 仅在北京搜索
poiSearchOption.boundary = @"region(北京,0)"; 
	
// 以坐标 (39.901268, 116.9403854) 为中心，返回就近地点搜索结果;
poiSearchOption.boundary = @"region(北京, 0, 39.901268, 116.9403854)";
```

#### 2. 通过`setBoundaryByRegionWithCityName:`方法指定参数：

```objC
// 在北京搜索
[poiSearchOption setBoundaryByRegionWithCityName:@"北京" autoExtend:YES];
setBoundaryByRegionWithCityName:(NSString *)cityName autoExtend:(BOOL)isAutoEntend;
	
// 在北京以坐标 (39.901268, 116.9403854) 为中心点搜索
[option setBoundaryByRegionWithCityName:@"北京" autoExtend:YES center:CLLocationCoordinate2DMake(39.901268, 116.9403854)];
```

#### 3. 指定地区搜索示例：
指定在北京地区搜索餐厅，示例如下：

```objC
poiSearchOption.boundary = @"region(北京,0)";
poiSearchOption.keyword  = @"餐厅";
[self.mySearcher searchWithPoiSearchOption: poiSearchOption];
```

下图仅在地图上标记第一页的搜索：

<img src="../images/search-poi.png" width="60%">
	
<br>

###周边搜索
<hr>
周边搜索参数与指定地区搜索参数类似，同样是使用`boundary`属性指定，不同的是周边搜索使用的关键字为`nearby`。周边搜索指定一个中心坐标，一个半径，以这两个参数构成的圆形区域进行搜索。

#### 1. 以字符串形式配置参数
参数格式：`nearby([lat,lng], radius<半径/米>, [auto_extend])`

参数说明：

| 参数 | 说明
| --- | ---
| lat,lng | 搜索结果以此坐标为中心，返回就近地点。
| radius | 半径，最大支持1000米 
| auto_extend | 可选参数，当前范围无结果时，是否自动扩大范围，取值为0或1。<br>取值1，默认值，自动扩大范围；<br>取值0，不扩大范围。

示例如下：

```objC
// 以 (39.908491, 116.374328) 为中心，搜索半径500米范围
poiSearchOption.boundary = @"nearby(39.908491,116.374328,500,0)";
```
#### 2. 通过`setBoundaryByNearbyWithCenterCoordinate: radius: autoExtend:`方法指定参数

```objC
// 以 (39.908491,116.374328) 为中心点，在搜索半径为500米
[poiSearchOption setBoundaryByNearbyWithCenterCoordinate:CLLocationCoordinate2DMake(39.908491, 116.374328)
                                                  radius:10
                                              autoExtend:NO];
```

#### 3. 周边搜索示例：
指定坐标(39.908491, 116.374328)为中心点，半径500米范围内搜索餐厅，示例如下：

```objC
poiSearchOption.boundary = @"nearby(39.908491, 116.374328, 500, 0)";
poiSearchOption.keyword  = @"餐厅";
[self.mySearcher searchWithPoiSearchOption: poiSearchOption];
```

下图仅在地图上标记第一页的搜索结果：

<img src="../images/search-poi-nearby.png" width="60%">

<br>

###矩形搜索
<hr>
矩形搜索参数与周边搜索参数类似，同样是使用`boundary`属性指定，不同的是矩形搜索使用的关键字为`rectangle`，并使用两个坐标构成的矩形区域进行搜索。

#### 1. 以字符串形式配置参数
参数格式：`boundary=rectangle(lat1, lng1<左下/西南>, lat2, lng2<右上/东北>)`

参数说明：

| 参数 | 说明
| --- | ---
| lat1,lng1 | 左下即西南方向的坐标
| lat2,lng2 | 右上即东北方向的坐标

示例如下：

```objC
// 在左下角 (39.908491, 116.374328)，右上角(39.918491, 116.384328)的矩形内搜索
poiSearchOption.boundary = @"rectangle(39.908491, 116.374328, 39.918491, 116.384328)";
```
#### 2. 通过`setBoundaryByRectangleWithleftBottomCoordinate: rightTopCoordinate:`方法指定参数

```objC
[poiSearchOption setBoundaryByRectangleWithleftBottomCoordinate:CLLocationCoordinate2DMake(39.908491,116.374328)
                                             rightTopCoordinate:CLLocationCoordinate2DMake(39.918491,116.384328)];
```

#### 3. 矩形搜索示例：
指定西南(39.907293,116.368935)和东北(39.914996,116.379321)坐标构成的矩形范围搜索餐厅，示例如下：

```objC
[poiSearchOption setBoundaryByRectangleWithleftBottomCoordinate:CLLocationCoordinate2DMake(39.907293,116.368935)
                                             rightTopCoordinate:CLLocationCoordinate2DMake(39.914996,116.379321)];

poiSearchOption.keyword = @"餐厅";

[self.mySearcher searchWithPoiSearchOption: poiSearchOption];
```

下图仅在地图上标记第一页的搜索结果：

<img src="../images/search-poi-rect.png" width="60%">

