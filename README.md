# OasisSDK

[![CI Status](https://img.shields.io/travis/jianchengpan/OasisSDK.svg?style=flat)](https://travis-ci.org/jianchengpan/OasisSDK)
[![Version](https://img.shields.io/cocoapods/v/OasisSDK.svg?style=flat)](https://cocoapods.org/pods/OasisSDK)
[![License](https://img.shields.io/cocoapods/l/OasisSDK.svg?style=flat)](https://cocoapods.org/pods/OasisSDK)
[![Platform](https://img.shields.io/cocoapods/p/OasisSDK.svg?style=flat)](https://cocoapods.org/pods/OasisSDK)


OasisSDK 为第三方应用提供了简单易用的绿洲API调用服务，使第三方客户端直接通过绿洲官方客户端分享动态。

## 安装

* 通过Cocoapods安装

1. 先安装Cocoapods；
2. 通过 pod repo update 更新OasisSDK的cocoapods版本。
3. 在Podfile对应的target中，添加pod 'OasisSDK'，并执行pod install --verbose。
4. 在项目中使用CocoaPods生成的.xcworkspace运行工程。
5. 在你的代码头文件中引入头文件 #import "OasisSDK.h"

## 使用

### 设置app

* 为了使绿洲客户端在处理请求后返回你的app，你需要在Scheme列表中添加一个scheme,scheme格式为 "wb"+"你的appKey",例如appkey为"123456",则scheme为"wb123456"。appKey为你在微博开放平台注册app时，为你分配的AppKey。

*  为了检测绿洲app是否已经安装，你需要在info.plist中添加以下设置:
```
    key>LSApplicationQueriesSchemes</key>
	<array>
		<string>oasis</string>
	</array>
```

### 启动sdk

在使用SDK的功能之前首先需要注册你的app信息,你的app信息由配置类（OasisConfig）来进行收集。收集的app信息主要包括appKey(你在微博开发平台注册app的appKey)。

```

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    OasisConfig *config = [OasisConfig configWith:@"你的appID"];
    config.debug = YES;
    [OasisSDK registerAPP:config];    
    return YES;
}

```

在正式上线以前，可以把OasisConfig的debug属性设置为true,以便在控制台中查看错误信息

### 分享动态

#### 发送请求

在分享动态时，首先实例化分享请求(OasisShareRequest),并添加需要分享的信息:

```
    //实例化
    OasisShareRequest *req = [OasisShareRequest new];
    //设置分享标题（可选）
    req.title = @"分享标题";
    //设置分享文字内容
    req.content = @"分享内容";

    //添加媒体信息
    OasisImageObject *image = [OasisImageObject new];
    image.imagaData = data;
    if(![req append:image]){
        //添加媒体失败
    }


    if(![OasisSDK sendReq:req])
    {
        //发送请求失败
    }

```

分享动态时必须提供媒体信息,现在支持的媒体信息有图片和视频两种,一个动态只能包含其中一种媒体信息,并且动态可添加的媒体有数量上限。
媒体的数据提供方式有NSData和相册(PHAsset)两种方式,通过data交换数据时，data有大小限制

#### 添加图片
图片媒体信息由OasisImageObject类表示.

```

OasisImageObject *image = [OasisImageObject new];

//通过 data交换媒体数据
image.imagaData = data;

//通过PHAsset 交换数据
//image.asset = asset;

[req append:image]

```

#### 添加视频

视频媒体信息由OasisVideoObject类表示.

```

OasisImageObject *image = [OasisVideoObject new];

//通过 data交换媒体数据
video.videoData = data;
video.fileExtension = ".mov"; //视频data需要提供正确的文件扩展名

//通过PHAsset 交换数据
//video.asset = asset;

[req append:image]

```

#### 处理响应

在绿洲处理完请求过后,会通过注册的scheme("wb"+appkey)回传数据，你需要在以下方法中处理响应信息:

```

//iOS9以前
-(BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url{
    id<OasisSDKDelegate> delegate = XXXX
    return [OasisSDK handleOpenUrl:url delegate:delegate];
}

//iOS9 以后
-(BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options{

    id<OasisSDKDelegate> delegate = XXXX
    return [OasisSDK handleOpenUrl:url delegate:delegate];
}

```
