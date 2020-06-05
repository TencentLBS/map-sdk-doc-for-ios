# 离线地图
<hr>

地图SDK 4.3.5版本中新增了离线地图功能，通过下载指定城市的离线地图，可以在设备处于离线模式时仍然可以显示该区域的地图。

<br>

## 开启离线地图

离线地图功能默认情况下处于关闭状态，需要通过mapView新增的接口打开该功能：

```objc
self.mapView.offlineMapEnable = YES;
```

<br>

## 下载离线地图

1. 离线地图QOfflineMap类采用单例模式调用，首次使用离线地图之前，需要调用`- (void)reloadItems:`方法来重建离线地图数据，否则离线地图数据可能为空值：

	```objc
	[[QOfflineMap sharedOfflineMap] reloadItems:^(BOOL setUpSuccess) {
		NSLog(@"setup:%d", setUpSuccess);
	}];
	```
	
2. 获取离线文件列表，包括全国概要、直辖市、省市、城市信息：

	```objc
	NSArray *items = [QOfflineMap sharedOfflineMap].items;
	```

	+ 该方法会返回包含了QOfflineItem元素或QOfflineProvince的数组，这两个类分别包含了城市、省市的信息
	+ QOfflineItem属性列表如下：

		| 属性 | 说明
		| --- | ---
		| NSString *name | 城市名称
		| NSString *pinyin | 城市名称的拼音
		| long long size | 该城市地图的数据包大小
		| QOfflineItemStatus status | 离线文件状态，包含：<br>QOfflineItemStatusNone：文件不存在<br>QOfflineItemStatusUpdated：文件已更新<br>QOfflineItemStatusOutdated：文件已过期
	+ QOfflineProvince是QOfflineItem的子类，在其基础上添加了`items`属性用于保存该省份的所有城市信息

		| 属性 | 说明
		| --- | ---
		| NSArray <QOfflineItem *> *items | 省份包含的所有城市离线文件信息
		
3. 获取离线文件列表之后，可以根据需要下载指定城市的离线文件，以北京市为例：

	```objc
    // 取出北京
    NSArray *items = [[QOfflineMap sharedOfflineMap] items];
    QOfflineItem *bjItem;
    for (QOfflineItem *item in items) {
        if ([item.name isEqualToString:@"北京市"]) {
            bjItem = item;
        }
        break;
    }
    
    [[QOfflineMap sharedOfflineMap] downloadItem:bjItem callback:^(QOfflineItem * _Nonnull downloadItem, QOfflineItemDownloadStatus status, id  _Nonnull info) {
        
        if (status == QOfflineItemDownloadNone) {
            NSLog(@"无下载项目");
        } else if (status == QOfflineItemDownloading) {
            NSLog(@"下载中");
        } else if (status == QOfflineItemDownloadCompleted) {
            NSLog(@"下载完成");
        } else {
            NSLog(@"下载失败");
        }
    }];
	```

<br>

## 取消下载

正在下载的离线地图数据，可以通过以下接口来取消地图的下载：

```objc
[[QOfflineMap sharedOfflineMap] cancelDownload:item];
```

调用了下载方法的离线地图数据，会自动添加到下载列表中，可以通过以下接口来获取正在下载的离线地图数据：

```objc
@property (nonatomic, readonly) NSArray <QOfflineItem *> *downloadingItems;
```

<br>

## 删除离线文件

已经下载完毕的离线地图数据，可以通过以下接口来删除该区域的地图数据：

```objc
[[QOfflineMap sharedOfflineMap] deleteItem:item];
```