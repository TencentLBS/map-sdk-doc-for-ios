## 地图视野控制

地图以中心点为准显示内容，通过设置中心点来移动地图的当前视野。同时可以调整显示的范围，以及限制显示的范围。

本章节分为以下三个小结：

* [基础视野操作](./camera-basic.md)
* [视野范围设置](./camera-rect.md)
* [限制显示区域](./basicMap-limit)

<br>

## 基础视野操作

腾讯地图SDK的基础视野操作包含：

1. 地图中心点
2. 缩放级别
3. 旋转角度
4. Overlooking(倾斜)
5. 视野动画操作

<br>

### 设置地图中心点
<hr>
地图始终以中心点为准显示，可以通过调整中心点的坐标来移动地图的显示范围，示例代码如下：

```objC
// 将地图中心点移动到腾讯北京总部
[self.mapView setCenterCoordinate:CLLocationCoordinate2DMake(40.040219, 116.273348)];
```

还可以通过以下地图的`centerCoordinate`属性，实时获取当前地图的中心点坐标，示例代码如下：

```objC
// 获取地图中心点的坐标
CLLocationCoordinate2D centerCoordinate = self.mapView.centerCoordinate;
```

<br>

### 地图缩放级别
<hr>
地图缩放级别相关属性如下：

| 属性 | 说明
| --- | ---
| CGFloat zoomLevel | 地图的缩放级别，范围 [minZoomLevel, maxZoomLevel]
| CGFloat minZoomLevel | 最小缩放级别, 默认 minZoomLevel = 3
| CGFloat maxZoomLevel | 最大缩放级别, 默认 maxZoomLevel = 20

地图显示的默认缩放级别为13，可以通过以下方法来调整地图的缩放等级：

```objC
// 设置缩放级别为15
[self.mapView setZoomLevel:15];
```

通过minZoomLevel和maxZoomLevel可以限制地图的最小、最大缩放级别：

```objC
// 将地图缩放级别限制在 [11, 14]
[self.mapView setMinZoomLevel:11 maxZoomLevel:14];
```

注意：

1. 如果设置了minZoomLevel和maxZoomLevel，手势操作地图缩放级别，将无法超过这个区间。
2. 通过`setZoomLevel:`方法设置的缩放级别小于minZoomLevel或大于maxZoomLevel，则实际调整的级别就为minZoomLevel和maxZoomLevel。

<br>

### 旋转角度
<hr>
旋转角度的范围是[0.f 360.f]，单位为角度，以顺时针为正向。可以通过调用`setRotation:`方法来调整。示例代码如下：

```objC
// 调整地图旋转角度为180度
[self.mapView setRotation:180];
```

<br>

### Overlooking(倾斜)
<hr>
倾斜角度范围为[0.f, 45.f]，单位为角度，可以通过调用`setOverlooking:`方法来调整。示例代码如下：

```objC
// 调整地图倾斜角度为30度
[self.mapView setOverlooking:30];
```

<br>

### 视野动画操作
<hr>
腾讯地图SDK提供了动画形式过渡的视野操作，示例如下：

设置地图中心点：

```objC
[self.mapView setCenterCoordinate:CLLocationCoordinate2DMake(40.040219, 116.273348) animated:YES];
```

设置缩放级别：

```objC
[self.mapView setZoomLevel:15 animated:YES];
```

调整旋转角度：

```objC
[self.mapView setRotation:180 animated:YES];
```

调整倾斜：

```objC
[self.mapView setOverlooking:30 animated:YES];
```