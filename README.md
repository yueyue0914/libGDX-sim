# libGDX-示例项目说明
## 构造介绍
多端打包公用一个核心与资源文件夹core/src/com/badlogic/*/*.java **是项目创建时候创建的文件夹

### 配置类型（DESKTOP桌面版）
根据要求，可以配置不同的入门类。我们将从桌面项目开始。在 （或在 Eclipse 下） 打开类。我们想要一个 800x480 的窗口并将标题设置为“拖放”。代码应如下所示：DesktopLauncher.javadesktop/src/…drop-desktop
```java
package com.badlogic.drop.desktop;

import com.badlogic.gdx.backends.lwjgl3.Lwjgl3Application;
import com.badlogic.gdx.backends.lwjgl3.Lwjgl3ApplicationConfiguration;
import com.badlogic.drop.Drop;

public class DesktopLauncher {
   public static void main (String[] arg) {
      Lwjgl3ApplicationConfiguration config = new Lwjgl3ApplicationConfiguration();
      config.setTitle("Drop");
      config.setWindowedMode(800, 480);
      config.useVsync(true);
      config.setForegroundFPS(60);
      new Lwjgl3Application(new Drop(), config);
   }
}
```
### 配置类型（android端）
转到 Android 项目，我们希望应用程序以横向模式运行。为此，我们需要在（或）根目录中进行修改，如下所示：AndroidManifest.xmlandroiddrop-android
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.badlogic.drop.android"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk android:minSdkVersion="8" android:targetSdkVersion="20" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/GdxTheme" >
        <activity
            android:name="com.badlogic.drop.android.AndroidLauncher"
            android:label="@string/app_name"
            android:screenOrientation="landscape"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

#### 参与贡献

1.  蔡昊麟
2.  张永朋



#### 特技

1.  使用 Readme\_XXX.md 来支持不同的语言，例如 Readme\_en.md, Readme\_zh.md
2.  Gitee 官方博客 [blog.gitee.com](https://blog.gitee.com)
3.  你可以 [https://gitee.com/explore](https://gitee.com/explore) 这个地址来了解 Gitee 上的优秀开源项目
4.  [GVP](https://gitee.com/gvp) 全称是 Gitee 最有价值开源项目，是综合评定出的优秀开源项目
5.  Gitee 官方提供的使用手册 [https://gitee.com/help](https://gitee.com/help)
6.  Gitee 封面人物是一档用来展示 Gitee 会员风采的栏目 [https://gitee.com/gitee-stars/](https://gitee.com/gitee-stars/)
