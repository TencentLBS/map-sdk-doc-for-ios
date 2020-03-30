## 截屏功能

iOS地图SDK在QMapView中提供了便捷的截屏功能，可以将当前的地图窗口截图为UIImage类型的图片数据，使用的接口如下：

```objC
/**
 *  @brief  异步截图 (默认超时时间为2.5秒)
 *
 *  @param rect 范围
 *  @param completion 异步block结果
 */
- (void)takeSnapshotInRect:(CGRect)rect completion:(void (^)(UIImage *resultImage))completion;

/**
 *  @brief  异步截图
 *
 *  @param rect 范围
 *  @param timeout 超时时间.
 *  @param completion 异步block结果
 */
- (void)takeSnapshotInRect:(CGRect)rect timeout:(CFTimeInterval)timeout completion:(void (^)(UIImage *resultImage))completion;
```

注意：截屏功能中rect参数，是以地图窗口的左上角为(0, 0)。