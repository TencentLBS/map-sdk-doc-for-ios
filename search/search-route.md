##路线规划

###简介

iOS 地图 SDK 提供的路线规划功能支持多种交通方式的路线计算能力，包括：

1. 驾车（driving）：支持结合实时路况、少收费、不走高速等多种偏好，精准预估到达时间（ETA）；
2. 步行（walking）：基于步行路线规划；
3. 公交（transit）：支持公共汽车、地铁等多种公共交通工具的换乘方案计算；

<br>

###请求参数概览
<hr>
####1. 驾车
QMSDrivingRouteSearchOption参数列表如下：

<style> table th:first-of-type { width: 30%; } </style>

| 属性 | 说明
| :-: | ---
| NSString \*from | 起点坐标格式: from=lat<纬度>,lng<经度>
| NSString \*from_poi | 起点POI ID，传入后，优先级高于from（坐标）, 样例: 4077524088693206111
| NSString \*from_track | 起点轨迹，可通过`setTrackPoints`生成，格式样例：40.037029,116.316633,16,500,160….
| NSString \*to | 终点坐标格式: to=lat<纬度>,lng<经度>
| NSString \*to_poi | 终点POI ID（可通过腾讯位置服务地点搜索服务得到），当目的地为较大园区、小区时，会以引导点做为终点（如出入口等），体验更优；<br>该参数优先级高于to（坐标），但是当目的地无引导点数据或POI ID失效时，仍会使用to（坐标）作为终点样例: 4077524088693206111
| NSString \*policy | 路线规划条件参考一下枚举值: <br>LEAST\_TIME: 表示速度优先；<br>LEAST\_FEE: 表示费用优先；<br>LEAST\_DISTANCE: 表示距离优先；<br>REAL\_TRAFFIC: 表示根据实时路况计算最优路线
| NSString \*waypoints | 途径点，元素类型为CLLocationCoordinate2D的NSValue类型
| NSNumber \*heading | 在起点位置时的车头方向，数值型，取值范围0至360（0度代表正北，顺时针一周360度）传入车头方向，对于车辆所在道路的判断非常重要，直接影响路线计算的效果
| NSNumber \*speed | from辅助参数，速度，单位：米/秒，默认3。 当速度低于1.39米/秒时，heading将被忽略
| NSNumber \*accuracy | from辅助参数，定位精度，单位：米，取>0数值，默认5。 当定位精度>30米时heading参数将被忽略
| NSString \*plate_number | 车牌号，填入后，路线引擎会根据车牌对限行区域进行避让，不填则不不考虑限行问题

####2. 步行
QMSWalkingRouteSearchOption参数列表如下：

| 属性 | 说明
| :-: | ---
| NSString \*from | 起点坐标 格式：from=lat&lt;纬度&gt;,lng&lt;经度&gt;
| NSString \*to | 终点坐标 格式：to=lat&lt;纬度&gt;,lng&lt;经度&gt;

####3. 公交
QMSBusingRouteSearchOption参数列表如下：

| 属性 | 说明
| :-: | ---
| NSString \*from | 起点坐标 格式：from=lat&lt;纬度&gt;,lng&lt;经度&gt;
| NSString \*to | 终点坐标 格式：to=lat lat&lt;纬度&gt;,lng&lt;经度&gt;
| NSString \*policy | 路线规划优先条件（非必填）<br>排序策略，以下三选一：<br>policy=LEAST\_TIME：时间短（默认）<br>policy=LEAST\_TRANSFER：少换乘<br>policy=LEAST\_WALKING：少步行<br>额外限制条件，可与排序策略配合使用，如：policy=LEAST\_TRANSFER,NO\_SUBWAY：NO\_SUBWAY，不坐地铁
| NSDate *departure_time | 出发时间，用于过滤掉非运营时段的线路，不传时默认使用当前时间(非必填)

<br>

###发起检索
<hr>
####1. 发起驾车路线检索
构建驾车路线检索请求参数，然后调用QMSSearcherAPI中的`searchWithDrivingRouteSearchOption:`发起驾车路线检索：

```objC
QMSDrivingRouteSearchOption *drivingOpt = [[QMSDrivingRouteSearchOption alloc] init];
[drivingOpt setPolicyWithType:QMSDrivingRoutePolicyTypeLeastTime];
[drivingOpt setFrom:@"39.983906,116.307999"];
[drivingOpt setTo:@"39.979381,116.314128"];

[self.mySearcher searchWithDrivingRouteSearchOption:drivingOpt];
```

####2. 发起步行路线检索
构建步行路线检索请求参数，然后调用QMSSearcherAPI中的`searchWithWalkingRouteSearchOption:`发起步行路线检索：

```objC
QMSWalkingRouteSearchOption *walkingOpt = [[QMSWalkingRouteSearchOption alloc] init];
[walkingOpt setFrom:@"39.983906,116.307999"];
[walkingOpt setTo:@"39.979381,116.314128"];

[self.mySearcher searchWithWalkingRouteSearchOption:walkingOpt];
```

####3. 发起公交路线检索
构建公交路线检索请求参数，然后调用QMSSearcherAPI中的`searchWithBusingRouteSearchOption:`发起公交路线检索

```objC
QMSBusingRouteSearchOption *busOpt = [[QMSBusingRouteSearchOption alloc] init];
[busOpt setFrom:@"40.015109,116.313543"];
[busOpt setTo:@"40.151850,116.296881"];
[busOpt setPolicyWithType:QMSBusingRoutePolicyTypeLeastTime];

[self.mySearcher searchWithBusingRouteSearchOption:busOpt];
```

<br>

###返回结果
<hr>
####1. 驾车检索返回结果
当检索成功后，会调用到`searchWithDrivingRouteSearchOption: didReceiveResult:`回调函数，通过解析 QMSDrivingRouteSearchResult 获取路径数据：

```objC
- (void)searchWithDrivingRouteSearchOption:(QMSDrivingRouteSearchOption *)drivingRouteSearchOption didRecevieResult:(QMSDrivingRouteSearchResult *)drivingRouteSearchResult
{
    self.drivingRouteResult = drivingRouteSearchResult;

    NSLog(@"Result: %@", self.drivingRouteResult);
}
```

QMSDrivingRouteSearchResult类属性说明：

| 属性 | 说明
| :-: | ---
| QMSResultCode status | 状态码, 0为成功
| NSString \*message | 状态说明
| NSArray \*routes | 路径规划方案数组, 元素类型为QMSRoutePlan

QMSRoutePlan类属性说明：

| 属性 | 说明
| :-: | ---
| NSString \*mode | 方案交通方式
| CGFloat distance | 方案整体距离 单位:米
| CGFloat duration | 方案估算时间 单位:分钟 四舍五入
| NSString \*direction | 方案整体方向描述
| NSArray \*polyline | 方案路线坐标点串, 导航方案经过的点, 每个step中会根据索引取得自己所对应的路段,类型为encode的CLLocationCoordinate2D。具体获取方式可参考demo
| NSArray \*steps | 标记如何通过一个路段的信息，类型为QMSRouteStep

QMSRouteStep类属性说明：

| 属性 | 说明
| :-: | ---
| NSString \*instruction | 阶段路线描述
| NSString \*road_name | 阶段路线路名(非必有)
| NSString \*dir_desc | 阶段路线方向(如"南", 非必有)
| CGFloat distance | 阶段路线距离 单位:米
| CGFloat duration | 时间 单位:分钟 四舍五入
| NSString \*act_desc | 阶段路线末尾动作(如 ”向南转“, 非必有)
| NSArray \*polyline_idx | 阶段路线坐标点串在方案路线坐标点串的位置<br>从经纬度数组中 根据索引查询这一段路的途经点。 在WebService原始接口做了除2处理, 数据类型为NSNumber。<br>polyline\_idx[0]:起点索引<br>polyline\_idx[1]:终点索引

**QMSRouteStep中一重要属性需说明：**

 + NSArray *polyline\_idx：阶段路线坐标点串在方案路线坐标点串的位置，从经纬度数组中，根据索引查询这一段路的途经点。在WebService原始接口做了除2处理, 数据类型为NSNumber。polyline\_idx[0]:起点索引，polyline\_idx[1]:终点索引。

获取到的起点索引和终点索引代表着 QMSRoutePlan类中的 NSArray *polyline 相应元素索引的坐标点串值。

####2. 步行检索返回结果
当检索成功后，会调用到`searchWithWalkingRouteSearchOption: didReceiveResult:`回调函数，通过解析 QMSWalkingRouteSearchResult 获取路线数据：

```objC
- (void)searchWithWalkingRouteSearchOption:(QMSWalkingRouteSearchOption *) walkingRouteSearchOption didRecevieResult:(QMSWalkingRouteSearchResult *) walkingRouteSearchResult
{
    self.walkingRouteResult = walkingRouteSearchResult;

    NSLog(@"Result: %@", self.walkingRouteResult);
}
```

步行的返回结果数据结构与驾车基本一致，返回的数据可查询QMSSearchResult.h文件。

####3. 公交检索返回结果
当检索成功后，会调用到 searchWithBusingRouteSearchOption: didReceiveResult: 回调函数，通过解析 QMSBusingRouteSearchResult 获取路径数据：

```objC
- (void)searchWithBusingRouteSearchOption:(QMSBusingRouteSearchOption *) busingRouteSearchOption didRecevieResult:(QMSBusingRouteSearchResult *) busingRouteSearchResult
{
    self.busRouteResult = busingRouteSearchResult;

    // 获取第一个路线
    NSLog(@"bus result is: %@", self.busRouteResult.routes[0]);
}
```

QMSBusingRouteSearchResult类属性说明：

| 属性 | 说明
| :-: | ---
| NSArray *routes | 路径规划方案数组, 元素类型QMSBusingRoutePlan，可在routes中获取不同的路线

QMSBusingRoutePlan类属性说明：

| 属性 | 说明
| :-: | ---
| CGFloat distance | 距离，单位:米
| CGFloat duration | 时间，单位:分钟 四舍五入
| NSString \*bounds | 路线bounds，用于显示地图时使用
| NSArray \*steps | 分段描述，类型为：QMSBusingSegmentRoutePlan，从属性 steps 中可获得公交分段方案（QMSBusingSegmentRoutePlan），解析后的数据可用于在地图上绘制路线。

QMSBusingSegmentRoutePlan类属性说明：

| 属性 | 说明
| :-: | ---
| NSString \*mode | 标记路径规划类型 "DRIVING":驾车 "WALKING":步行 "TRANSIT":公交
| CGFloat distance | 距离 单位:米
| CGFloat duration | 时间 单位:分钟 四舍五入
| CGFloat price | 阶段路线所花费用 单位:元
| NSString \*direction | 方向描述
| NSArray \*polyline | 方案路线坐标点串, 导航方案经过的点, 每个step中会根据索引取得自己所对应的路段,类型为encode的CLLocationCoordinate2D
| NSArray \*lines | 同个路段多趟车的选择, 元素类型QMSBusingRouteTransitLine
| NSArray \*polyline | 解析后的数据可以用于绘制分段的polyline，具体获取方式可参考demo

QMSBusingRouteTransitLine类说明：

| 属性 | 说明
| :-: | ---
| NSString \*vehicle | 交通工具
| NSString \*id_ | id
| CGFloat distance | 距离(米)
| NSTimeInterval duration | 预计耗时(分钟)
| NSString \*title | 标题
| NSArray \*polyline | 途经点数组，类型为encode的CLLocationCoordinate2D，具体获取方式可参考demo
| NSInteger station_count | 经停站数目
| NSArray&lt;QMSBusStation \*&gt; \*stations | 上车站数组，元素类型为QMSBusStation
| QMSStationEntrance \*destination | 目的地地址
| QMSBusStation \*geton | 上车站
| QMSBusStation \*getoff | 下车站

QMSBusStation，QMSStationEntrance等类详情请参考 QMSSearchResult.h 文件。

<br> 

###调用示例
<hr>
路线规划的接口用请参考 [demo](https://lbs.qq.com/ios_v1/download_3d.html)。
