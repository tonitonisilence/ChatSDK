# ChatSDK
a sdk for chat
# 简介
DirectChatSDK是一个app端客服系统访客解决方案，既包含了客服聊天逻辑管理，也提供了聊天界面，开发者可方便的将客服功能集成到自己的 APP 中。
# 集成
只需3部就可以将SDK集成到你项目里面
### 一.添加SDK进你的项目
1. Android Studio:需要将SDK当做module添加到project里
```javascript
implementation project(':DirectChatSDK')
``` 
2. 在你的Project的build.gradle中加入
```javascript
buildscript {
    
    repositories {
        google()
        jcenter()
    }
    dependencies {
        ...//你自己的配置
        
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' //添加配置
        classpath 'com.jakewharton:butterknife-gradle-plugin:8.5.1' //添加配置
        
    }
}
```
### 二.在你的 Application 类的 onCreate 函数中，加入以下初始化代码：
```Java
public class SDKDemoApplication extends Application{

    public int mFinalCount;//记录在应用是否在前台

    @Override
    public void onCreate() {
        super.onCreate();

        initSDK();
        registerCallBack();
    }
     /**
     * 初始化SDK
     */
    private void initSDK(){
        String userId = "applicationuserid";
        String deviceId = "applicatiodeviceId";
        DCSCenterManager.getInstance(this).init(new DCSInitModel().newBuilder()
                .setUserId(userId)//用户id,未登录不传或者传null
                .setCity("广东省广州市")//定位城市名称(xx省xx市)
                .setDeviceId(deviceId)//设备号码*必传
                .setTitleBackgroundResId(com.bonatone.chatsdk.R.color.dcs_colorTop)//标题栏背景色
                .setTitleTextColorResId(com.bonatone.chatsdk.R.color.dcs_colorWhite)//标题栏字体颜色
                .setMenuBackgroundResId(com.bonatone.chatsdk.R.color.dcs_colorTop)//右侧菜单背景颜色
                .setMenuTextColorResId(com.bonatone.chatsdk.R.color.dcs_colorWhite)//右侧按钮文本颜色
                .setBackArrowIconResId(com.bonatone.chatsdk.R.drawable.dcs_ic_back)//返回按钮图标
                .setIsDarkStatusBarText(false)//是否深色通知栏主题
                .setUserPhotoPath("")//用户头像，url地址
                .setServiceTitleString("在线客服")//在线客服标题
                .build());
    }

    //判断应用是否在前后台
    private void registerCallBack() {
        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
            @Override
            public void onActivityCreated(Activity activity, Bundle savedInstanceState) {

            }

            @Override
            public void onActivityStarted(Activity activity) {
                mFinalCount++;
                //如果mFinalCount ==1，说明是从后台到前台
                if (mFinalCount == 1) {
                    //上一次可能为聊天页面或者聊天里面选择页面，需要把推送状态置为false
                    DCSCenterManager.getInstance(getApplicationContext()).pushOff();
                }
            }

            @Override
            public void onActivityResumed(Activity activity) {

            }

            @Override
            public void onActivityPaused(Activity activity) {

            }

            @Override
            public void onActivityStopped(Activity activity) {
                mFinalCount--;
                //如果mFinalCount ==0，说明是前台到后台
                if (mFinalCount == 0) {
                    //上一个可能为聊天页面或者聊天里面选择页面，需要把推送状态置为true
                    DCSCenterManager.getInstance(getApplicationContext()).pushOn();
                }
            }

            @Override
            public void onActivitySaveInstanceState(Activity activity, Bundle outState) {

            }

            @Override
            public void onActivityDestroyed(Activity activity) {

            }
        });
    }
}
```
### 三.在你需要用到的地方调用SDK提供的界面
```Java
startActivity(new Intent(SDKDemoActivity.this, DCSMainActivity.class));//去到帮助与反馈首页
startActivity(new Intent(this, DCSChatActivity.class));//去到在线客服详情页
```
如果你的个别设置项需要更改也可以这样
```Java
//只修改其中的几项
DCSCenterManager.getInstance(SDKDemoActivity.this)
         .setTitleBackgroundResId(com.bonatone.chatsdk.R.color.dcs_colorWhite)
         .setTitleTextColorResId(com.bonatone.chatsdk.R.color.dcs_colorBlack)
         .setMenuBackgroundResId(com.bonatone.chatsdk.R.color.dcs_colorWhite)//右侧菜单背景颜色
         .setMenuTextColorResId(com.bonatone.chatsdk.R.color.dcs_colorBlack)//右侧按钮文本颜色
         .setServiceTitleString("更改在线客服标题")
         .setDarkStatusBarText(true);
```
通知栏点击跳转
```Java
startActivity(new Intent(this, DCSChatActivity.class).addFlags(Intent.FLAG_ACTIVITY_NEW_TASK));//去到在线客服详情页
```
如果你的应用没开启过，需要先启动你自己的界面，你也可以这样，可以在前面放置多个
```Java
startActivities(new Intent[]{new Intent(this, DCSMainActivity.class).addFlags(Intent.FLAG_ACTIVITY_NEW_TASK),
                new Intent(this, DCSChatActivity.class).addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
                });
```    
你可以获取未读信息数量，跟最后一条信息的内容,因为需要从服务端获取最新，所以需要增加异步
```Java
DCSCenterManager.getInstance(this).getNoReadMessage(new DCSGetNoReadCallBack() {
            @Override
            public void onSuccess(DCSQueryMessageModel dcsQueryMessageModel) {
                ((TextView) findViewById(R.id.text_message)).setText("未读数：" + dcsQueryMessageModel.getCount() +
                        "\n最后一条信息：" + dcsQueryMessageModel.getLast_message_content() +
                        "\n最后条信息时间：" + dcsQueryMessageModel.getLast_message_time() +
                        "\n最后条信息时间戳：" + dcsQueryMessageModel.getLast_message_currentTimeMillis());
            }
        });
```

# 混淆
在你的proguard-rules.pro加入
```Java
#module
-keep class com.bonatone.chatsdk.model.**{*;}
#butterknife
-keep class butterknife.** { *; }
-dontwarn butterknife.internal.**
-keep class **$$ViewBinder { *; }

#greenDAO 3
-keepclassmembers class * extends org.greenrobot.greendao.AbstractDao {
public static java.lang.String TABLENAME;
}
-keep class **$Properties

#BaseRecyclerViewAdapterHelper
-keep class com.chad.library.adapter.** {
*;
}
-keep public class * extends com.chad.library.adapter.base.BaseQuickAdapter
-keep public class * extends com.chad.library.adapter.base.BaseViewHolder
-keepclassmembers  class **$** extends com.chad.library.adapter.base.BaseViewHolder {
     <init>(...);
}

#okhttp
-dontwarn okhttp3.**
-dontwarn okio.**
-dontwarn javax.annotation.**
-dontwarn org.conscrypt.**
# A resource is loaded with a relative path so the package of this class must be preserved.
-keepnames class okhttp3.internal.publicsuffix.PublicSuffixDatabase
```
