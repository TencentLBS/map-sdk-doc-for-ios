##小车平滑移动组件

###简介
在使用LBS服务的应用中，越来越多的行业场景都需要地图SDK具备连续性的轨迹展示能力。例如出行、运动、物流、生活服务等类别的App中常常需要展示车辆或用户的行程轨迹、实时移动的位置数据。开发者展示轨迹的核心诉求是对真实场景的模拟，即实现轨迹的平滑展示以及关注目标在轨迹沿线的平滑移动、朝向改变。


<br>

###平滑移动类介绍
<hr>
小车平滑移动功能主要依赖于地图组件中的 QMUAnnotationAnimator 类和 &lt;QMULocation&gt; 协议。

####&lt;QMULocation&gt;协议
小车在折线段中移动时，需要提供构建该折线的坐标集合，每个集合都是一个实现了 &lt;QMULocation&gt; 协议的数据模型类，自定义类示例如下：

```
@interface CarLocation : NSObject <QMULocation>

// 坐标信息
@property (nonatomic, assign) CLLocationCoordinate2D coordinate;

@end
```

####QMUAnnotationAnimator平滑移动动画类
该类提供了平移动画的控制方法，方法介绍如下：

```
/**
 * @brief  执行平滑移动动画. 该方法支仅持地图在2D正北状态下进行.
 * @param annotationView  平滑移动的对象
 * @param locations       平滑移动需要经过的经纬度坐标点串
 * @param duration        平滑移动时间
 * @param needRotate      平滑移动过程中annotationView是否需要沿移动方向执行旋转动画
 */
+ (void)translateWithAnnotationView:(QAnnotationView *)annotationView
                          locations:(NSArray<id <QMULocation> > *)locations
                           duration:(CFTimeInterval)duration
                      rotateEnabled:(BOOL)needRotate;
```

该方法接收的locations参数，为实现了 &lt;QMULocation&gt; 协议的位置信息数据对象的集合，如果`needRotate = YES`, 当调用该方法时，annotationView 会沿着指定的坐标集合构建的折线移动。

**注意：该方法支仅持地图在2D正北状态下进行，暂时不支持在地图旋转、倾斜的情况下进行。**

<br>

###使用说明
<hr>
1. 平滑移动组件支持传入地图SDK的QAnnotationView或其子类，通过设置经纬度坐标点串和移动时间，使得该View可以按照点串路线进行平滑动画移动。

2. 若设置needRotate为YES，则在移动过程中，View自身的方向也会根据路线进行调整，使得View的上部与移动方向保持一致。

3. 若需要在动画过程中停止动画，可调用View的layer进行操作。

####示例展示

<img src="../images/utils-car.gif" width="60%"/>