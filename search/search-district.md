##行政区划

QMSSearcherAPI提供了三个行政区划相关查询接口，封装了腾讯地图 WebService API 的行政区划查询功能，更详细的接口描述可以查阅[官网](https://lbs.qq.com/webservice_v1/guide-region.html)。这里对 SDK 提供的行政区划查询接口做简要描述。

| 接口名 | 功能
| --- | ---
| searchWithDistrictSearchSearchOption: | 根据关键词搜索行政区划
| searchWithDistrictListSearchOption: | 发起全国行政区域检索
| searchWithDistrictChildrenSearchOption: | 发起子级行政区域检索

<br>

###构建行政区划搜索参数
<hr>
####QMSDistrictSearchSearchOption关键词检索参数
构建关键字行政区域检索参数 QMSDistrictSearchSearchOption，其中 keyword 为必填字段：

```objC
QMSDistrictSearchSearchOption *distOpt = [[QMSDistrictSearchSearchOption alloc] init];
[distOpt setKeyword:@"北京"];
```
参数说明：

| 参数 | 说明
| --- | ---
| NSString *keyword | 搜索关键词：<br>支持输入一个文本关键词：keyword=北京<br>支持多个行政区划代码，英文逗号分隔：keyword=130681,419001

####QMSDistrictListSearchOption全国行政区域检索参数
该参数无必填字段，初始化后即可发起行政区域检索。

```objC
QMSDistrictListSearchOption *listOpt = [[QMSDistrictListSearchOption alloc] init];
```

####QMSDistrictChildrenSearchOption子级行政区域检索参数
构建子级行政区域检索参数QMSDistrictChildrenSearchOption，id为非必填字段，缺省时返回最顶级行政区划：

```objC
QMSDistrictChildrenSearchOption *childOpt = [[QMSDistrictChildrenSearchOption alloc] init];
[childOpt setID:@"110000"];
```

参数说明：

| 参数 | 说明
| --- | ---
| NSString *ID | 父级行政区划ID，缺省时则返回最顶级行政区划

<br>

###发起行政区域检索
<hr>
调用QMSSearcherAPI中的`searchWithDistrictSearchSearchOption:`发起关键词行政区域检索

```objC
[self.mySearcher searchWithDistrictSearchSearchOption:distOpt];
```

调用QMSSearcherAPI中的`searchWithDistrictListSearchOption:`发起全国行政区域检索

```objC
[self.mySearcher searchWithDistrictListSearchOption:listOpt];
```

调用QMSSearcherAPI中的`searchWithDistrictChildrenSearchOption:`发起子级行政区域检索

```objC
[self.mySearcher searchWithDistrictChildrenSearchOption:childOpt];
```

<br>

###在回调中处理搜索数据
当检索成功后，会调用到`searchWithDistrictSearchOption: didReceiveResult:`回调函数，通过解析QMSDistrictSearchResult 数据把所需的结果绘制到地图上。

```objC
- (void)searchWithDistrictSearchOption:(QMSDistrictBaseSearchOption *)districtSearchOption didRecevieResult:(QMSDistrictSearchResult *)districtSearchResult
{
    self.distResult = districtSearchResult;
    NSLog(@"%@",self.distResult);
}
```

####QMSDistrictSearchResult类属性说明：

| 属性 | 说明
| --- | ---
| NSArray\<NSArray\<QMSDistrictData \*\> \*\> \*result | 结果数组：<br>第0项，代表一级行政区划；<br>第1项，代表二级行政区划，以此类推；<br>使用getchildren接口时，仅为指定父级行政区划的子级；<br>元素类型包含QMSDistrictData元素的数组

####QMSDistrictData类属性说明：

| 属性 | 说明
| --- | ---
| NSString *id_ | 行政区划唯一标识；注：省直辖地区，在数据表现上有一个重复的虚拟节点（其id最后两位为99），其目的是为了表明省直辖关系而增加的，开发者可根据实际需要选用
| NSString *name | 简称，如“内蒙古”
| NSString *fullname | 全称，如“内蒙古自治区”
| CLLocationCoordinate2D location | 中心点坐标(经纬度)
| NSArray\<NSString \*\> \*pinyin | 行政区划拼音，每一下标为一个字的全拼<br>如：["nei","meng","gu"]
| NSArray\<NSNumber \*\> \*cidx | 子级行政区划在下级数组中的下标位置

获取检索结果result里的数据可以参考demo

<br>

###效果示例
<hr>
####关键词检索行政区划
使用 QMSDistrictSearchSearchOption 参数搜索 “北京”：

```objC
QMSDistrictSearchSearchOption *distOpt = [[QMSDistrictSearchSearchOption alloc] init];
[distOpt setKeyword:@"北京"];
[self.mySearcher searchWithDistrictSearchSearchOption:distOpt];
```

<img src="../images/search-district.png" width="60%">

####全国行政区划检索
使用 QMSDistrictListSearchOption 参数搜索：

```objC
QMSDistrictListSearchOption *listOpt = [[QMSDistrictListSearchOption alloc] init];
[self.mySearcher searchWithDistrictListSearchOption:listOpt];
```

<img src="../images/search-district-list.png" width="60%">

####子级行政区划检索
使用 QMSDistrictChildrenSearchOption 参数搜索"110000"：

```objC
QMSDistrictChildrenSearchOption *childOpt = [[QMSDistrictChildrenSearchOption alloc] init];
[childOpt setID:@"110000"];
[self.mySearcher searchWithDistrictChildrenSearchOption:childOpt];
```

<img src="../images/search-district-children.png" width="60%">

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