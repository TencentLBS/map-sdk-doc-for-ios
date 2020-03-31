##手动配置

从[官网](https://lbs.qq.com/ios_v1/download_3d.html)下载最新版本后

这里我们提供XCode的腾讯地图SDK工程配置方法。

### 1. 添加framework

开发者需在[腾讯地图 iOS SDK官网](https://lbs.qq.com/ios_v1/download_3d.html)下载sdk资源包，解压后在sdk文件夹中找到QMapKit.framework文件，然后在工程界面选中  **library文件夹**  右键弹出菜单中选择"Add Files To..."，从**文件夹sdk** 中将文件QMapKit.framework添加到工程中，在弹出窗口中勾选"Copy items into destination group's folder(if needed)" 。

<img src="../images/intro-manual2.png" width="60%"/>

### 2. 添加资源文件

在工程界面右键弹出菜单中选择"Add Files To..."，从文件夹sdk（同步骤1）->QMapKit.framework中将资源文件QMapKit.bundle添加到工程中，在弹出窗口中勾选"Copy items into destination group's folder(if needed)" 。

<img src="../images/intro-manual3.png" width="60%"/>

添加需要的编译选项：在TARGETS-Build Settings-Other Linker Flags 中添加如下内容： -ObjC 。其中，**O**和**C**大写。

<img src="../images/intro-manual4.png" width="60%"/>


地图SDK依赖以下库： libsqlite3.tbd、libc++.tbd、QMapKit.framework。

最终链接库配置如下：

<img src="../images/intro-manual1.png" width="60%"/>


### 3. 注意事项

如果没有正确添加资源文件，则有可能出现地图加载不成功现象，如下图：

<img src="../images/intro-manual5.png" width="60%"/>



