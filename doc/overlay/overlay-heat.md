##热力图覆盖层

###简介

热力图是一种瓦片覆盖层，它能够在地图上表达数据的密度和分布状况。下面是一张截图效果：

<img src="../images/overlay-heat.png" width="400px">

开发者可以通过自定义热力图的渐变颜色和其他属性，来调整自己需要的样式。下面我们将分三部分，帮助开发者熟悉热力图的配置：

1. 添加一个简单的热力图
2. 自定义热力图样式
3. 移除热力图

<br>

###添加一个简单的热力图
<hr>
为了添加一个简单的热力图，你需要提前准备好带有坐标分布的数据集。基本使用步骤如下：

1. 准备热力节点需要的坐标分布数据，示例如下：

	```objC
	// 纬度 经度 权值
	116.895579	24.306521	43
	```

2. 遍历所有数据创建QHeatTileNode热力节点对象，保存在数组中，然后添加到mapView中，示例如下：

	```objC
	self.nodes = [NSMutableArray array];
	
	// 将所有的热力节点数据保存在数组中遍历    
	[dataStringArray enumerateObjectsUsingBlock:^(NSString *  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
	    
	    NSArray *ar = [obj componentsSeparatedByCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
	    
	    // 纬度.
	    double lat = [ar[1] doubleValue];
	    // 经度.
	    double lon = [ar[0] doubleValue];
	    // 权值.
	    double val = [ar[2] doubleValue];
	    
	    QHeatTileNode *node = [[QHeatTileNode alloc] init];
	    // 热力节点坐标
	    node.coordinate = CLLocationCoordinate2DMake(lat, lon);
	    // 热力节点权值
	    node.value      = val;
	    
	    [self.nodes addObject:node];
	}];
	    
	QHeatTileOverlay *heat = [[QHeatTileOverlay alloc] initWithHeatTileNodes:self.nodes];
	```

3. 最后，实现&lt;QMapViewDelegate&gt;的`mapView: viewForOverlay:`方法，创建QHeatTileOverlayView，示例如下：

	```objC
	- (QOverlayView *)mapView:(QMapView *)mapView viewForOverlay:(id<QOverlay>)overlay
	{
	    if ([overlay isKindOfClass:[QHeatTileOverlay class]]) {
	        QHeatTileOverlayView *render = [[QHeatTileOverlayView alloc] initWithTileOverlay:overlay];
	        return render;
	    }
	    return nil;
	}
	```
4. 效果图如下

	<img src="../images/overlay-heat1.png" width="400px">

<br>

###设置热力图样式
<hr>

QHeatTileOverlay提供一些属性来满足自定义样式的热力效果，包括：衰变半径、透明度、颜色梯度。为了能够正常显示新的图层，改变样式之后需要重新reload图层。

QHeatTileOverlay为热力图的数据对象，下表为属性说明：

| 属性 | 说明
| --- | ---
| NSInteger decayRadius | 衰变半径，单位：point。<br>范围越大计算耗时越长，默认20。建议100以内。
| CGFloat opacity | 透明度，默认为0.6，范围：0-1。
| QHeatTileGradient *gradient | 颜色梯度

QHeatTileGradient为热力图的颜色梯度对象，下表为属性说明：

| 属性 | 说明
| --- | ---
| NSArray&lt;UIColor \*&gt; \*colors | 颜色变化数组
| NSArray&lt;NSNumber \*&gt; \*startPoints | 颜色变化节点，需为严格递增数组，区间为[0, 1.0]

更改属性基本使用方法如下：

1. 更改热力图颜色梯度

	```objC
	// 自定义的色值
	self.heatTileOverlay.gradient = [[QHeatTileGradient alloc] initWithColor:@[[UIColor grayColor],[UIColor brownColor], [UIColor blueColor],[UIColor greenColor],[UIColor yellowColor],[UIColor redColor]] andWithStartPoints:@[@(0.1),@(0.3),@(0.5), @(0.6), @(0.8),@(0.9)]];
	```
	
2. 设置热力半径

	```objC
	// 设置热力图半径
	[self.heatTileOverlay setDecayRadius:50];
	```

3. 调用QHeatTileOverlayView的`reloadData`方法，刷新热力图：

	```objC
	QHeatTileOverlayView *heatView = (QHeatTileOverlayView *)[self.mapView viewForOverlay:self.heatTileOverlay];
	[heatView reloadData];
	```
	
4. 示例图如下：

	<img src="../images/overlay-heat-custom.png" width="400px">