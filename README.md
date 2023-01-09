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
已经为我们填写了正确值的设置工具设置为“横向”。如果我们想在纵向模式下运行游戏，我们会将该属性设置为“纵向”。android:screenOrientation

我们还希望节省电池并禁用加速度计和指南针。我们在 （或） 中的文件中执行此操作，该文件应如下所示：AndroidLauncher.javaandroid/src/…drop-android
```java
package com.badlogic.drop.android;

import android.os.Bundle;

import com.badlogic.gdx.backends.android.AndroidApplication;
import com.badlogic.gdx.backends.android.AndroidApplicationConfiguration;
import com.badlogic.drop.Drop;

public class AndroidLauncher extends AndroidApplication {
   @Override
   protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      AndroidApplicationConfiguration config = new AndroidApplicationConfiguration();
      config.useAccelerometer = false;
      config.useCompass = false;
      initialize(new Drop(), config);
   }
}
```
我们无法定义 的分辨率 ，因为它是由 Android 操作系统设置的。如前所述，我们只需将 800x480 目标分辨率缩放到设备的任何分辨率。Activity.

### 配置类型（HTML5端）
最后，确保HTML5项目也使用800x480的绘图区域。为此，我们修改了（或）中的文件：HtmlLauncher.javahtml/src/…drop-html
```java
package com.badlogic.drop.client;

import com.badlogic.gdx.ApplicationListener;
import com.badlogic.gdx.backends.gwt.GwtApplication;
import com.badlogic.gdx.backends.gwt.GwtApplicationConfiguration;
import com.badlogic.drop.Drop;

public class HtmlLauncher extends GwtApplication {
   @Override
   public GwtApplicationConfiguration getConfig () {
      return new GwtApplicationConfiguration(800, 480);
   }

   @Override
   public ApplicationListener createApplicationListener () {
      return new Drop();
   }
}
```
### 所有主游戏的代码都应该在Core/src/com/badlogic/*/*.java，资源在主界面的assest资源文件中！

### 开始
#### 第一步：加载资源
在项目生成的时候已经自动生成了Core.java文件(*这里的Core.java是根据libGDZX创建项目时变化)
我们的第一个任务是加载资产并存储对它们的引用。资产通常在方法中加载，所以让我们这样做：ApplicationAdapter.create()
```java
package com.badlogic.drop;

import com.badlogic.gdx.ApplicationAdapter;
import com.badlogic.gdx.Gdx;
import com.badlogic.gdx.audio.Music;
import com.badlogic.gdx.audio.Sound;
import com.badlogic.gdx.graphics.Texture;

public class Drop extends ApplicationAdapter {
   private Texture dropImage;
   private Texture bucketImage;
   private Sound dropSound;
   private Music rainMusic;

   @Override
   public void create() {
      dropImage = new Texture(Gdx.files.internal("droplet.png"));
      bucketImage = new Texture(Gdx.files.internal("bucket.png"));

      
      dropSound = Gdx.audio.newSound(Gdx.files.internal("drop.wav"));
      rainMusic = Gdx.audio.newMusic(Gdx.files.internal("rain.mp3"));

      
      rainMusic.setLooping(true);
      rainMusic.play();

     
   }
```
对于我们的每个资产，我们在类中都有一个字段，以便我们稍后可以引用它。该方法中的前两行加载雨滴和桶的图像。A 表示存储在视频内存中的加载图像。通常不能绘制到纹理。通过将 a 传递给资产文件到其构造函数来加载 A。此类实例是通过 提供的方法之一获得的。有不同类型的文件，我们在这里使用“内部”文件类型来指代我们的资产。内部文件位于 Android 项目的目录中。如前所述，桌面和 HTML5 项目引用相同的目录。Dropcreate()TextureTextureFileHandleFileHandleGdx.filesassets

接下来，我们加载音效和背景音乐。libGDX 区分存储在内存中的音效和从存储位置流式传输的音乐。音乐通常太大而无法完全保存在内存中，因此存在差异。根据经验，如果您的样本短于 10 秒，则应使用实例，而较长的音频片段应使用实例。SoundMusic

#### 第二步 相机和SpriteBatch
接下来，我们要创建一个摄像机和一个.我们将使用前者来确保无论实际屏幕分辨率如何，我们都可以使用 800x480 像素的目标分辨率进行渲染。这是一个特殊的类，用于绘制 2D 图像，就像我们加载的纹理一样。SpriteBatchSpriteBatch
我们在类中添加了两个新字段，我们称它们为 camera 和 batch：
```java
   private OrthographicCamera camera;
   private SpriteBatch batch;
```
在create()方法中，我们首先创建如下所示的相机：
```java
   camera = new OrthographicCamera();
   camera.setToOrtho(false, 800, 480);
```
这将确保摄像机始终向我们显示游戏世界中 800x480 单位宽的区域。把它想象成一个进入我们世界的虚拟窗口。我们目前将这些单位解释为像素，以使我们的生活更轻松一些。但是，没有什么可以阻止我们使用其他单位，例如仪表或您拥有的任何东西。相机非常强大，允许您执行许多我们在本基本教程中不会介绍的事情。有关详细信息，请查看开发人员指南的其余部分。

接下来我们创建（仍在create()方法中）：SpriteBatch
```java
   batch = new SpriteBatch();
```
#### 第三步 添加存储桶（接雨滴的桶）
缺少的最后一位是我们的水桶和雨滴的表示。让我们考虑一下我们需要在代码中表示它们：

水桶/雨滴在我们的 800x480 单位世界中具有 x/y 位置。
水桶/雨滴有宽度和高度，以我们世界的单位表示。
桶/雨滴具有图形表示，我们已经以加载的实例的形式拥有它们。Texture
因此，要描述水桶和雨滴，我们需要存储它们的位置和大小。libGDX 提供了一个我们可以用于此目的的类。让我们首先创建一个代表我们的存储桶。我们添加一个新字段：Rectangle Rectangle
```java
   // add this import and NOT the one in the standard library
   import com.badlogic.gdx.math.Rectangle;

   private Rectangle bucket;
```
在create()方法中，我们实例化矩形并指定其初始值。我们希望存储桶位于屏幕底部边缘上方 20 像素，并水平居中。
```java
   bucket = new Rectangle();
   bucket.x = 800 / 2 - 64 / 2;
   bucket.y = 20;
   bucket.width = 64;
   bucket.height = 64;
```
我们将桶水平居中，并将其放置在屏幕底部边缘上方 20 像素处。等等，为什么设置为20，不应该是480-20吗？默认情况下，libGDX（和 OpenGL）中的所有渲染都是在 y 轴朝上的情况下执行的。桶的 x/y 坐标定义桶的左下角，绘图的原点位于屏幕的左下角。矩形的宽度和高度设置为 64x64，这是我们目标分辨率高度的一小部分。bucket.y


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
