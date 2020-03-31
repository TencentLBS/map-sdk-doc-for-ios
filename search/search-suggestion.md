##关键词输入提示

###简介

关键词输入提示 `searchWithSuggestionSearchOption:` 接口，封装了腾讯地图 WebService API 的关键字提示功能，关键字提示检索是指根据用户输入的关键词，给出对应的提示信息，将最有可能的搜索词呈现给用户，减少用户输入信息，大大提升用户体验。如：输入“北京”，提示“北京站”，“北京西站”等。效果示例图如下：

<br>

<img src="../images/search-keyword.png" width="60%">

更详细的接口描述可以查阅[官网](https://lbs.qq.com/webservice_v1/guide-suggestion.html)。这里对 SDK 提供的地点搜索接口做简要描述。

<br>

###关键词输入提示参数说明
<hr>
####构建参数
设置关键字输入提示参数 QMSSuggestionSearchOption，其中 keyword 和 region 是必填字段：

```objC
QMSSuggestionSearchOption sugOption = [[QMSSuggestionSearchOption alloc] init];

sugOption.keyword = @"北京南";

sugOption.region = @"北京";
```

####其他参数说明

| 属性 | 说明
| --- | ---
| NSString *filter | 搜索指定分类，可搜索多个分类。语法请参考[官网](http://lbs.qq.com/webservice_v1/guide-search.html#boundary_detail )介绍。<br>举例：category=大学,中学。
| NSNumber *region_fix | 是否自动扩大搜索范围，取值为0或1。<br>取值0：默认值，当前城市无结果时，自动扩大范围到全国匹配；<br>取值1：固定在当前城市
| NSString *location | 定位坐标<br>传入后，若用户搜索关键词为类别词（如酒店、餐馆时），与此坐标距离近的地点将靠前显示。<br>格式：location=lat,lng
| NSNumber *get_subpois | 是否返回子地点，如大厦停车场、出入口等，取值为0或1；<br>取值0：默认值，不返回；<br>取值1：返回。
| NSNumber *policy | 检索策略，目前支持：<br>policy=0：默认，常规策略；<br>policy=1：本策略主要用于收货地址、上门服务地址的填写，提高了小区类、商务楼宇、大学等分类的排序，过滤行政区、道路等分类（如海淀大街、朝阳区等），排序策略引入真实用户对输入提示的点击热度，使之更为符合此类应用场景，体验更为舒适；<br>policy=10：出行场景（网约车） – 起点查询；<br>policy=11：出行场景（网约车） – 终点查询
| NSString *address_format | 可选值，示例:`address_format=short`，返回“不带行政区划的”短地址
| NSNumber *page_index | 页码，从1开始，最大页码需通过count进行计算，必须与page_size同时使用
| NSNumber *page_size | 每页条数，取值范围1-20，必须与page_index 时使用

<br>

###发起关键字提示检索
<hr>
1. 调用QMSSearcherAPI中的 `searchWithSuggestionSearchOption:` 发起关键字提示检索

	```objC
	[self.mySearcher searchWithSuggestionSearchOption:sugOption];
	```

2. 在回调中处理搜索数据
当检索成功后，会调用到 `searchWithSuggestionSearchOption: didReceiveResult:` 回调函数，通过解析 QMSSuggestionResult 数据把所需的结果绘制到地图上。

	```objC
	- (void)searchWithSuggestionSearchOption:(QMSSuggestionSearchOption *)suggestionSearchOption didReceiveResult:(QMSSuggestionResult *)suggestionSearchResult
	{
	    self.sugResult = suggestionSearchResult;
	
	    NSLog(@"suggest result:%@", suggestionSearchResult);
	}
	```

	在回调中，可通过 QMSSuggestionResult 中的 dataArray 获取 QMSSuggestionPoiData 对象，从中得到POI数据。

	```
	// 获取第一个POI数据
	QMSSuggestionPoiData *poiData = [suggestionSearchResult.dataArray objectAtIndex:0];
	
	// 获取第一个subPOI数据
	QMSSuggestionSubPoiData *subPOIData = [suggestionSearchResult.sub_pois objectAtIndex:0];
	```
	
<br>

###检索结果参数说明
<hr>
####QMSSuggestionResult属性说明

| 属性 | 说明
| --- | ---
| NSUInteger count | 本次搜索结果总数。
| NSArray *dataArray | 提示词数组，每项为一个POI(QMSSuggestionPoiData)对象。
| NSArray *sub_pois | 子地点列表，仅在输入参数get_subpois=1时返回。<br>每项为一个POI(QMSSuggestionSubPoiData)对象。

####QMSSuggestionPoiData属性说明

| 属性 | 说明
| --- | ---
| NSString *id_ | POI唯一标识
| NSString *title | 提示文字
| NSString *address | 地址详细描述
| NSNumber *adcode | 邮政编码
| NSString *province | 省 
| NSString *city | 市
| QMSPoiType type | POI类型，值说明：<br>0：普通POI<br>1：公交车站<br>2：地铁站<br>3：公交线路<br>4：行政区划
| CLLocationCoordinate2D location | 坐标(经纬度)

####QMSSuggestionSubPoiData属性说明

| 属性 | 说明
| --- | ---
| NSString  *parent_id | POI唯一标识
| NSString *id_ | POI唯一标识
| NSString *title | 提示文字
| NSString *address | 地址详细描述
| CLLocationCoordinate2D location | 坐标(经纬度)
| NSNumber *adcode | 邮政编码
| NSString *city | 市

<br>

###效果示例
<hr>
示例代码如下：

```objC
QMSSuggestionSearchOption sugOption = [[QMSSuggestionSearchOption alloc] init];

sugOption.keyword = @"北京南";

sugOption.region = @"北京"

[self.mySearcher searchWithSuggestionSearchOption:sugOption];
```

提示输入检索结果的第一页数据：

<img src="../images/search-keyword1.png" width="60%">

点击选择北京南站后：

<img src="../images/search-keyword2.png" width="60%">

<br>

###错误信息回调
<hr>
当检索失败时，回调函数 `searchWithSearchOption: didFailWithError:` 会返回对应的错误信息

```objC
- (void)searchWithSearchOption:(QMSSearchOption *)searchOption didFailWithError:(NSError *)error
{
    NSLog(@"%@",error);
}
```

更详细设置请参考demo