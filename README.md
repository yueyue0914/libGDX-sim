# libGDX-示例项目说明
## 构造介绍
这里放整个项目的一些注意事项：
多端打包公用一个核心与资源文件夹core/src/com/badlogic/*/*.java **是项目创建时候创建的文件夹

### 配置类型
#### DESKTOP桌面版
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
#### Android端
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

#### HTML端
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

### Core部分构建
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


#### 第四步 渲染存储桶
是时候渲染我们的桶了。我们要做的第一件事是用深蓝色清除屏幕。只需将方法更改为如下所示：render()
```java
   @Override
   public void render() {
      ScreenUtils.clear(0, 0, 0.2f, 1);

      //... more to come here ...
   }
```
ScreenUtils.clear(r, g, b, a)的参数是该颜色的红色、绿色、蓝色和 alpha 分量，每个分量都在 [0， 1] 范围内。
接下来，我们需要告诉我们的相机以确保它已更新。照相机使用称为矩阵的数学实体，该实体负责设置用于渲染的坐标系。每次我们更改相机的属性（例如其位置）时，都需要重新计算这些矩阵。在我们的简单示例中，我们不会这样做，但通常最好每帧更新一次相机：
```java
   camera.update();
```
现在我们渲染我们的存储桶：
```java
   batch.setProjectionMatrix(camera.combined);
   batch.begin();
   batch.draw(bucketImage, bucket.x, bucket.y);
   batch.end();
```
第一行指示 使用相机指定的坐标系。如前所述，这是通过称为矩阵的东西完成的，更具体地说，是投影矩阵。该字段就是这样一个矩阵。从那里开始，将渲染前面描述的坐标系中的所有内容。SpriteBatchcamera.combinedSpriteBatch

接下来，我们告诉SpriteBatch开始一个新的批次。为什么我们需要这个，什么是批次？OpenGL最讨厌的就是告诉它单个图像。它希望被告知一次渲染尽可能多的图像。

这门课有助于让OpenGL开心。它将记录介于 和 之间的所有绘图命令。一旦我们调用，它将立即提交我们提出的所有绘图请求，从而大大加快渲染速度。这一切在开始时可能看起来很麻烦，但这就是以每秒 60 帧的速度渲染 500 个精灵和以每秒 20 帧的速度渲染 100 个精灵之间的区别。SpriteBatchSpriteBatch.begin()SpriteBatch.end()SpriteBatch.end()

#### 第五步 使存储桶移动（触摸/鼠标）
是时候让用户控制存储桶了。之前我们说过我们允许用户拖动存储桶。让我们让事情变得简单一点。如果用户触摸屏幕（或按下鼠标按钮），我们希望存储桶水平围绕该位置居中。将以下代码添加到方法的底部将执行此操作：render()
```java
   if(Gdx.input.isTouched()) {
      Vector3 touchPos = new Vector3();
      touchPos.set(Gdx.input.getX(), Gdx.input.getY(), 0);
      camera.unproject(touchPos);
      bucket.x = touchPos.x - 64 / 2;
   }
```
首先，我们通过调用询问输入模块当前是否触摸了屏幕（或按下了鼠标按钮）。接下来，我们要将触摸/鼠标坐标转换为相机的坐标系。这是必要的，因为报告触摸/鼠标坐标的坐标系可能与我们用于表示世界中对象的坐标系不同。Gdx.input.isTouched()

Gdx.input.getX()并返回当前的触摸/鼠标位置（libGDX 也支持多点触摸，但这是另一篇文章的主题）。为了将这些坐标转换为相机的坐标系，我们需要调用该方法，该方法请求一个三维向量。我们创建这样一个向量，设置当前的触摸/鼠标坐标并调用该方法。矢量现在将包含我们存储桶所在的坐标系中的触摸/鼠标坐标。最后，我们将存储桶的位置更改为以触摸/鼠标坐标为中心。Gdx.input.getY()camera.unproject()Vector3
##### 键盘移动
在桌面和浏览器中，我们还可以接收键盘输入。让我们在按下左光标键或右光标键时使存储桶移动。

我们希望存储桶以每秒 200 像素/单位的速度向左或向右移动，没有加速。为了实现这种基于时间的移动，我们需要知道在最后一个渲染帧和当前渲染帧之间经过的时间。以下是我们如何做到这一切：
```java
   if(Gdx.input.isKeyPressed(Input.Keys.LEFT)) bucket.x -= 200 * Gdx.graphics.getDeltaTime();
   if(Gdx.input.isKeyPressed(Input.Keys.RIGHT)) bucket.x += 200 * Gdx.graphics.getDeltaTime();
```
该方法告诉我们是否按下了特定键。枚举包含 libGDX 支持的所有键码。该方法返回上一帧和当前帧之间经过的时间（以秒为单位）。我们需要做的就是通过添加/减去 200 个单位乘以增量时间（以秒为单位）来修改存储桶的 x 坐标。Gdx.input.isKeyPressed()KeysGdx.graphics.getDeltaTime()

我们还需要确保我们的存储桶保持在屏幕限制范围内：
```java
   if(bucket.x < 0) bucket.x = 0;
   if(bucket.x > 800 - 64) bucket.x = 800 - 64;
```

#### 第六步 添加雨滴
对于雨滴，我们保留了一个实例列表，每个实例都跟踪雨滴的位置和大小。让我们将该列表添加为字段：Rectangle
```java
   private Array<Rectangle> raindrops;
```
该类是一个 libGDX 实用程序类，用于代替标准 Java 集合，如 。后者的问题在于它们以各种方式产生垃圾。该类试图尽可能减少垃圾。libGDX还提供其他垃圾收集器感知集合，例如哈希映射或集合。ArrayArrayListArray

我们还需要跟踪上次生成雨滴的时间，因此我们添加了另一个字段：
```java
   private long lastDropTime;
```
我们将以纳秒为单位存储时间，这就是我们使用 long 的原因。

为了便于创建雨滴，我们将编写一个名为 实例化一个新的 ，将其设置为屏幕顶部边缘的随机位置并将其添加到数组中。spawnRaindrop()Rectangleraindrops
```java
   private void spawnRaindrop() {
      Rectangle raindrop = new Rectangle();
      raindrop.x = MathUtils.random(0, 800-64);
      raindrop.y = 480;
      raindrop.width = 64;
      raindrop.height = 64;
      raindrops.add(raindrop);
      lastDropTime = TimeUtils.nanoTime();
   }
```
该方法应该是不言自明的。该类是一个libGDX类，提供各种与数学相关的静态方法。在这种情况下，它将返回一个介于 0 和 800 - 64 之间的随机值。这是另一个libGDX类，它提供了一些非常基本的与时间相关的静态方法。在这种情况下，我们以纳秒为单位记录当前时间，稍后我们将根据该时间决定是否生成新的下降。MathUtilsTimeUtils

在该方法中，我们现在实例化雨滴数组并生成我们的第一个雨滴：create()

我们需要在create()方法中实例化该数组：
```java
   raindrops = new Array<Rectangle>();
   spawnRaindrop();
```
接下来，我们在render()方法中添加几行，用于检查自生成新雨滴以来已经过去了多少时间，并在必要时创建一个新雨滴：
```java
   if(TimeUtils.nanoTime() - lastDropTime > 1000000000) spawnRaindrop();
```
我们还需要让我们的雨滴移动，让我们采取简单的路线，让它们以每秒 200 像素/单位的恒定速度移动。如果雨滴在屏幕底部边缘下方，我们将其从阵列中删除。
```java
   for (Iterator<Rectangle> iter = raindrops.iterator(); iter.hasNext(); ) {
      Rectangle raindrop = iter.next();
      raindrop.y -= 200 * Gdx.graphics.getDeltaTime();
      if(raindrop.y + 64 < 0) iter.remove();
   }
```
雨滴需要渲染。我们将它添加到SpriteBatch渲染代码中，现在如下所示：
```java
   batch.begin();
   batch.draw(bucketImage, bucket.x, bucket.y);
   for(Rectangle raindrop: raindrops) {
      batch.draw(dropImage, raindrop.x, raindrop.y);
   }
   batch.end();
```
最后一个调整：如果雨滴击中桶，我们希望播放我们的水滴声音并从阵列中删除雨滴。我们只需将以下行添加到雨滴更新循环中：
```java
      if(raindrop.overlaps(bucket)) {
         dropSound.play();
         iter.remove();
      }
```
该方法检查此矩形是否与另一个矩形重叠。在我们的例子中，我们告诉 drop 声音效果自行播放并从数组中删除雨滴。Rectangle.overlaps()

#### 第七步 清理
用户可以随时关闭应用程序。对于这个简单的例子，不需要做任何事情。但是，一般来说，对操作系统稍作帮助并清理我们创建的混乱是一个好主意。

任何实现接口并因此具有方法的 libGDX 类都需要在不再使用后手动清理。在我们的示例中，纹理、声音和音乐以及 .作为好公民，我们按如下方式覆盖该方法：Disposabledispose()SpriteBatchApplicationAdapter.dispose()
```java
   @Override
   public void dispose() {
      dropImage.dispose();
      bucketImage.dispose();
      dropSound.dispose();
      rainMusic.dispose();
      batch.dispose();
   }
```
释放资源后，不应以任何方式访问它。

一次性资源通常是不由 Java 垃圾回收器处理的本机资源。这就是我们需要手动处理它们的原因。libGDX 提供了多种方式来帮助进行资产管理。阅读开发指南的其余部分以发现它们。

#### 其他 处理暂停和恢复
Android 具有每次用户接到电话或按下主页按钮时暂停和恢复应用程序的表示法。在这种情况下，libGDX会自动为您做很多事情，例如重新加载可能丢失的图像（OpenGL上下文丢失，这本身就是一个可怕的主题），暂停和恢复音乐流等等。

在我们的游戏中，没有真正需要处理暂停/恢复。一旦用户返回应用程序，游戏就会从离开的地方继续。通常，人们会实现暂停屏幕并要求用户触摸屏幕以继续。这是留给读者的练习 - 查看和方法。ApplicationAdapter.pause()ApplicationAdapter.resume()


## 参与贡献

1.  蔡昊麟
2.  张永朋



## 特技

1.  使用 Readme\_XXX.md 来支持不同的语言，例如 Readme\_en.md, Readme\_zh.md
2.  Gitee 官方博客 [blog.gitee.com](https://blog.gitee.com)
3.  你可以 [https://gitee.com/explore](https://gitee.com/explore) 这个地址来了解 Gitee 上的优秀开源项目
4.  [GVP](https://gitee.com/gvp) 全称是 Gitee 最有价值开源项目，是综合评定出的优秀开源项目
5.  Gitee 官方提供的使用手册 [https://gitee.com/help](https://gitee.com/help)
6.  Gitee 封面人物是一档用来展示 Gitee 会员风采的栏目 [https://gitee.com/gitee-stars/](https://gitee.com/gitee-stars/)
