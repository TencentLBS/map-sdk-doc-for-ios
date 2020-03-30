##检索功能概述及接入

地图 SDK 提供的检索能力依托于腾讯地图开放平台提供的 WebService API，所有的检索接口都有[配额限制](https://lbs.qq.com/webservice_v1/guide-quota.html)，如果您希望更大的配额，请注册腾讯地图开发者并付费购买所需的配额。在使用检索功能前，请先阅读 [WebService API](https://lbs.qq.com/webservice_v1/index.html) 入门指南。


###接入步骤
1. 在[腾讯地图控制台](https://lbs.qq.com/dev/console/user/info)的Key管理页面，进入想要开启检索功能的Key设置页面，并勾选WebServiceAPI功能：

	<img src="../images/webservice.png">

2. 在AppDelegate文件中引入头文件，并在`application: didFinishLaunchingWithOptions:`方法中设置apiKey

	```objC
	#import "AppDelegate.h"
	#import <QMapKit/QMSSearchServices.h>
	
	@implementation AppDelegate
	
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	    
	    // 启用检索功能
	    [QMSSearchServices sharedServices].apiKey = @"ApiKey";
	    
	    return YES;
	}
	
	@end
	```

3. 在使用检索功能的控制器中，引入QMSSearcher头文件，初始化QMSSearcher对象并指定当前控制器为代理：

	```objC
	#import <QMapKit/QMSSearcher.h>
	
	@interface KeywordHintViewController () <QMSSearchDelegate>
	@property (nonatomic, strong) QMSSearcher *mapSearcher;
	@end
	
	@implementation KeywordHintViewController
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	    
	    self.mapSearcher = [[QMSSearcher alloc] initWithDelegate:self];
	}
	```