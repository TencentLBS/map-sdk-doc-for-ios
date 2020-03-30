##手势回调

###简介

腾讯地图SDK会在点击地图或地标时、拖动地图时回调方法，开发者可以通过这些回调时机，做特殊状态下的逻辑。

<br>

###点击回调
<hr>
配置QMapView的delegate对象之后，并实现&lt;QMapViewDelegate&gt;的以下方法，当点击地图和POI时会调用该方法：

| 方法 | 说明
| --- | ---
| mapView: didTapAtCoordinate: | 当点击地图上任意地点时调用，方法会传入点击的坐标点
| mapView: didTapPoi: | 当点击地图上任意的POI时调用，方法会传入点击的POI信息，类型为QPoiInfo

QPoiInfo类型属性说明：

| 属性 | 说明
| --- | ---
| NSString *name | POI名字信息
| CLLocationCoordinate2D coordinate | POI的经纬度坐标

注意：如果同时实现了`mapView: didTapAtCoordinate:`和`mapView: didTapPoi:`方法，在点击POI时只会调用`mapView: didTapPoi:`方法。

<br>

###拖动回调
<hr>
当地图区域发生改变时，如果此次改变使用过手势触发时，会调用以下方法：

| 方法 | 说明
| --- | ---
| mapView: regionWillChangeAnimated: gesture: | 地图区域即将改变时会调用此接口
| mapView: regionDidChangeAnimated: gesture: | 地图区域改变完成后会调用此接口，如果是由手势触发，当触摸结束且地图region改变的动画结束后才会触发此回调

上述方法可以通过判断gesture参数是否为YES来判断方法是否由手势触发的，控制代码的执行。

