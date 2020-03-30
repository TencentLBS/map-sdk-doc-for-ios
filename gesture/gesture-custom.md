##自定义手势

###简介
腾讯地图SDK除了提供的基本手势，也可以使用系统原生API来添加自定义手势，本小结通过实例来介绍如何实现通过长按手势在地图上添加Annotation。

###自定义长按手势
<hr>
1. 创建长按手势，并将手势添加在mapView中：

	```objC
	UILongPressGestureRecognizer *longPressGestureRecognizer = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleLongPressAction:)];
	    
	[self.mapView addGestureRecognizer:longPressGestureRecognizer];
	```
	
2. 在手势的监听方法中，获取点击的屏幕坐标，并将其转化为地图中的坐标，并创建Annotation：

	```objC
	- (void)handleLongPressAction:(UIGestureRecognizer *)gestureRecognizer
	{
	    if (gestureRecognizer.state == UIGestureRecognizerStateBegan)
	    {
	        CGPoint point = [gestureRecognizer locationInView:self.mapView];
	        
	        CLLocationCoordinate2D coordinate = [self.mapView convertPoint:point toCoordinateFromView:self.mapView];
	        
	        QPointAnnotation *annotation = [[QPointAnnotation alloc] init];
	        annotation.coordinate = coordinate;
	        
	        [self.mapView addAnnotation:annotation];
	    }
	}
	```
	
<br>

###常见问题
<hr>
1. 在地图中添加了单击手势，与地图的点击手势产生冲突怎么办？

	> 为添加的手势设置代理，然后实现`<UIGestureRecognizerDelegate>`协议的`gestureRecognizer: shouldRecognizeSimultaneouslyWithGestureRecognizer:`方法，并返回YES，即可让地图同时处理多个手势。