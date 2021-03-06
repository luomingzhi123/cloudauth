# iOS集成 {#task_1426491 .task}

下载无线认证SDK后，您可参考以下步骤将认证SDK集成到您的iOS应用中。

[单击查看老版本接入文档说明。](../../../../cn.zh-CN/老系统文档说明/老版本接入文档说明.md#)

**说明：** iOS SDK与BundleID绑定，不同BundleID需要在[管理控制台](https://yundun.console.aliyun.com/?p=cloudauth)上重新下载SDK。

## 在工程中导入 SDK {#section_1d0_tas_sxy .section}

**说明：** 对于已经接入过实人认证无线SDK的接入方（iOS SDK版本号<=2.1，可在RPSDK.framework文件夹下info.plist文件中查看CFRPSDKVersion），在控制台下载到新版本的无线SDK后（iOS SDK版本号\>=2.2），需要将新下载SDK包中的所有的文件替换原来工程里的对应文件，否则会报错。

1.  解压无线认证SDK后，将以下iOS的依赖包引入到您的应用工程中： 
    -   RPSDK.framework
    -   FaceLivenessOpen.framework
    -   SecurityGuardSDK.framework
    -   SGMain.framework
    -   SGNoCaptcha.framework
    -   SGSecurityBody.framework
    -   AliyunOSSiOS.framework
    -   WindVane.framework
    -   WindVaneBasic.framework
    -   WindVaneBridge.framework
    -   WindVaneCore.framework
    -   ZipArchive.framework
    -   AliReachability.framework
2.  确认您的工程中已引入以下实人认证服务需要的系统依赖： 
    -   CoreMedia.framework
    -   CoreMotion.framework
    -   CoreTelephony.framework
    -   AVFoundation.framework
    -   ImageIO.framework
    -   MobileCoreServices.framework
    -   MediaPlayer.framework
    -   CoreLocation.framework
    -   AddressBook.framework
    -   AddressBookUI.framework
    -   SystemConfiguration.framework
    -   AudioToolbox.framework
    -   AssetsLibrary.framework
    -   Libresolv.tbd
    -   WebKit.framework
    -   Libiconv.tbd
    -   Libc++.tbd
    -   Libz.tbd
3.  在您的工程资源中（Copy Bundle Resources），引入无线认证SDK 包中的yw\_1222\_\*.jpg签名图片文件，以及resource目录下的FaceLivenessSDK.bundle和RPSDK.bundle文件。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135432/156713139653978_zh-CN.png)


4.  编译选项：在工程的Other Linker Flags选项中添加-ObjC。

## 使用SDK {#section_hc8_cxz_aok .section}

1.  初始化。 

    一般在应用启动时进行。

    ``` {#codeblock_6p9_xh3_81w}
    #import <RPSDK/RPSDK.h>
     [RPSDK initialize:RPSDKEnvOnline] //必须为RPSDKEnvOnline
    ```

2.  进入认证。 

    ``` {#codeblock_xiw_e0k_2eg}
    [RPSDK start:verifyToken rpCompleted:^(RPVerifyState verifyState, NSString *code) {
         NSLog(@"verifyResult = %ld",(unsigned long)verifyState);
         if(verifyState == RPVerifyStatePass) { //认证通过。
         }
         else if(verifyState == RPVerifyStateFail) { //认证不通过。
         }
         else if(verifyState == RPVerifyStateNotVerify) { //未认证，用户主动退出，或者姓名身份证号实名校验不匹配等原因，导致未完成认证流程。
         }
    
     }withVC:self.navigationController];
    ```

    **说明：** 

    -   verifyToken参数由接入方的服务端调用实人认证服务的DescribeVerifyToken接口获得。
    -   在RPSDK.start接口调用的回调会返回用户认证的各种状态，具体信息体现在verifyState和code参数取值，详见下表说明。
    |verifyState|code|code释义|
    |-----------|----|------|
    |RPVerifyStatePass|1|认证通过|
    |RPVerifyStateFail|取值3~12|表示认证不通过，具体的不通过原因可以查看服务端的查询认证结果（DescribeVerifyResult）接口文档中认证状态的表格说明|
    |RPVerifyStateNotVerify|-1|未完成认证，原因是：用户在认证过程中，主动退出|
    |RPVerifyStateNotVerify|3001|未完成认证，原因是：认证token无效或已过期|
    |RPVerifyStateNotVerify|3101|未完成认证，原因是：用户姓名身份证实名校验不匹配|
    |RPVerifyStateNotVerify|3102|未完成认证，原因是：实名校验身份证号不存在|
    |RPVerifyStateNotVerify|3103|未完成认证，原因是：实名校验身份证号不合法|
    |RPVerifyStateNotVerify|3104|未完成认证，原因是：认证已通过，重复提交|
    |RPVerifyStateNotVerify|3204|未完成认证，原因是：操作太频繁|


## 常见问题 {#section_jaw_7io_ww2 .section}

-   调起无线认证SDK进入认证页面时，提示**网络异常，请检查网络**、**UNKNOWN\_ERROR**或者出现空白页。

    处理建议：查看Xcode console。

    -   SG ERROR = 1208、1215、1411
        -   如果您已经接入过实人认证无线SDK，且iOS SDK版本号<=2.1，那么请到控制台下载新版本的无线SDK（iOS SDK版本号\>=2.2），并使用新下载SDK包中的所有的文件替换原来开发包里的对应文件。

            **说明：** 您可以RPSDK.framework文件夹下的info.plist文件中查看CFRPSDKVersion。

        -   如果确认不存在上述情况，则说明当前开发包与在管理控制台下载SDK时上传包的BundleID不一致。请在管理控制台重新上传当前开发包，并使用新下载SDK中的yw\_1222\_\*.jpg签名图片文件替换开发包中原有的文件。
    -   SG ERROR = 1412、1225

        说明图片不存在。请确保SDK中的yw\_1222\_\*.jpg签名图片正确地引入到了Copy Bundle Resource下。

-   项目中之前引入的组件与无线认证SDK中的组件（如SecurityGuardSDK、AliyunOSSiOS等）有重复。

    处理建议：

    -   若SecurityGuardSDK组件有重复，删除低版本，但要保留两个版本的yw\_1222\_\*.jpg签名图片文件。
    -   若AliyunOSSiOS等其他组件有重复，删除低版本即可。
-   项目中之前引入的SecurityEnvSDK组件与无线认证SDK中的SGMain组件报duplicate symbol冲突。

    处理建议：SecurityEnvSDK组件是SGMain组件的早先版本，删除SecurityEnvSDK，但要保留两个组件的yw\_1222\_\*.jpg签名图片文件。

-   出现编译失败报错。

    根据不同的报错信息，采取相应解决方案，具体说明如下。

    -   报错信息：**duplicate symbol \_OBJC\_CLASS\_$\_xxx** 

        处理建议：接入方修改调用NebulaWK的方式，去除NebulaWK的依赖，改用WindVane内部的NebulaWK。

    -   报错信息：**ld: symbol\(s\) not found for architecture arm64 \(\_OPEN\_SECURITYBODY\_ENV\_XXX\)** 

        处理建议：删除旧版本无线保镖，导入新版本库，并清空Xcode Derived Data，重新编译。

    -   报错信息：**Undefined symbols for architecture x86\_64:\_OBJC\_CLASS\_$\_RPSDK** 

        处理建议：添加以下预处理宏控制，仅在真机环境调用实人认证。

        ``` {#codeblock_t67_3oo_lkk}
         #if TARGET_IPHONE_SIMULATOR // 实人认证不支持模拟器调试
        
                          #elif TARGET_OS_IPHONE // 真机
                         // 实人认证初始化
                          [RPSDK initialize:RPSDKEnvOnline];
                          // 进入认证
                         // [RPSDK start:rpCompleted:]
                         #endif
        ```

-   出现运行崩溃报错。

    根据不同的报错信息，采取相应解决方案，具体说明如下。

    -   报错信息：**BUG IN CLIENT OF LIBLATFORM: Trying to recursively lock an os\_unfair\_lock** 

        处理建议：修改GrowingIO调用方式。`setTrackWebView(false)`全应用webview不抓取，针对需要抓取的webview，前去目标页面进行设置。

        ``` {#codeblock_n5a_ylm_clt}
         GrowingIO.startWithConfiguration(MainApplication.getInstance(),
                       new Configuration().setApp(MainApplication.getInstance())
                       .trackAllFragments()
                       .setTrackWebView(false)
                       .setHashTagEnable(true)
                       .setChannel(CommonUtil.getAppChannel(MainApplication.getInstance())));
        ```

    -   报错信息：**Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: ' setObjectForKey: object cannot be nil \(key: appName\)** 

        处理建议：必须设置CFBundleName，即应用名称。

-   接入后导致应用网络请求失败。

    处理建议：在RPSDK初始化以后，完成以下设置：

    ``` {#codeblock_bow_db5_tm3}
    [[NSUserDefaults standardUserDefaults] setObject:@YES forKey:[NSString stringWithFormat:@"WebKitAll%@ccessFro%@eURLs", @"owFileA", @"mFil"]];
    
    [[NSUserDefaults standardUserDefaults] setObject:@YES forKey:[NSString stringWithFormat:@"WebKitAll%@ccessFro%@eURLs", @"owUniversalA", @"mFil"]];
    ```

-   提示版本过低。

    处理建议：必须设置CFBundleName，即应用名称。

-   第一次认证会出现几秒钟的延迟。

    处理建议：Xcode10在调试模式下打开视频流时，会有10s左右延迟。断开调试模式后若无延时，则可以忽略此问题。

-   SG ERROR = 105、103。

    处理建议：在Build Settings的Other Linker Flags中添加**-ObjC**。


