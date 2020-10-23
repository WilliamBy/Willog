---
categories:
  - 技术
  - 笔记
article:
    # Code highlight settings
    highlight:
        # Code highlight themes
        theme: atom-one-light
        # Show copy code button
        clipboard: true
        # Default folding status of the code blocks. Can be "", "folded", "unfolded"
        fold: unfolded
    # Whether to show thumbnail image for every article
    thumbnail: true
    readtime: true
tags:
  - Android
  - Kotlin
widgets:
  - position: left
    type: toc
sidebar:
    # Left sidebar configurations
    left:
        # Whether the sidebar sticks to the top when page scrolls
        sticky: false
toc: true
thumbnail: 'https://gitee.com/BAIDI-CODER/PicGo/raw/master/img/20201020193149.jpg'
abbrlink: 87cffae2
date: 2020-10-17 00:00:00
---

{% label Android success %}
{% label Kotlin warning %}
{% label 读书笔记 info %}

<br>
<br>



<article class="message">
  <div class="message-header">
    <p>简介</p>
  </div>
  <div class="message-body">
      <p>"《第一行代码：Android 3rd》第九章和第十章的读书笔记，主要内容包括四大组件中的Service以及一些手机多媒体的使用。"</p>
  </div>
</article>
<!-- more -->

> 为方便笔记，约定使用**伪Kotlin语法**：
>
> `<!ClassName>` 用来表示ClassName类的一个实例
>
> `<Abstract>或<A>` 写在方法(类)前用来表示该方法（类）是一个必须要给出实现的抽象方法/类
>
> `<Static>或<S>`写在方法(类)前用来表示该方法（类）是一个静态的方法/类
>
> `<+>` 表示public方法
>
> `O` 是`override`的缩略
>
> `XXX` 表示待定的语法字符串
>
> `...` 表示前后代码段省略
>
> **UML 图**的规定：
>
> 斜体函数*为抽象函数
>
> 下划线函数$为静态函数
>
> 以下前缀代表访问权限
>
> - `+` Public
> - `-` Private
> - `#` Protected
> - `~` Package/Internal

## 丰富多彩的多媒体

### 使用通知

#### 概览

<div class="mermaid">
    classDiagram
      class NotificationManager {
      +notify()
      +createNotificationChannel()
      }
      class NotificationChannel
      class NotificationCompat {
      +Builder()
      }
      NotificationCompat --> Notification: 创建
      NotificationManager --> NotificationChannel: 创建、管理
      NotificationManager --> Notification: 发送
      Notification --* NotificationChannel: 对应
</div>

#### NotificationManager 类

- 创建：`val manager = getSystemService(Context.NOTIFICATION_SERVICE) as NotifictionManager`
  - 备注：`getSystemService()` 是 `Context` 的一个用来获取系统服务方法。

#### NotificationChannel 类

> 每条通知都要属于一个对应的渠道。每个应用程序都可以自由地创建当前应用拥有哪些通知渠道，但是这些通知渠道的控制权是掌握在用户手上的。用户可以自由地选择这些通知渠道的重要程度，是否响铃、是否振动或者是否要关闭这个渠道的通知。

``` kotlin 创建通知渠道
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {	
// 通知渠道是v8.0新增的API，需要做下版本判别才能使用NotificationChannel
    val channel = NotificationChannel(channelId: String, channelName: String, importance)
    //构造通知渠道实例                    渠道标识串          向用户显示的渠道名称    重要等级
    
    <!NotificationManager>.createNotificationChannel(channel)
    //创建通知渠道                                        
}
```

- 通知渠道重要等级：`IMPORTANCE_HIGH`、`IMPORTANCE_DEFAULT`、`IMPORTANCE_LOW`、`IMPORTANCE_MIN`，用不同level构造的channel会有不同的表现，如HIGH级别的渠道会弹出通知。

#### Notification 类

- 可以在 Activity（较少）、Broadcast、Service 中创建

``` kotlin 通知の构建与发送
//用AndroidX库的NotificationCompat类来构建通知要比原API兼容性更好
val notification = NotificationCompat.Builder(context, channelId: String)//通知要对应唯一一个channel
        .setContentTitle(title: String)
        .setContentText(text: String)
		.setContentIntent(pi: PendingIntent)//点击事件
        .setSmallIcon(R.drawable.small_icon)
        .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.large_icon))
		.setAutoCancel(true)//点击通知后通知消失
        //可以连缀多个set方法来赋予通知各种属性
        .build()//构建
        
//通过管理器发送
<!NotificationManager>.notify(id: Int, notification: Notification)
//							  通知的标识符    通知
```

- 通知属性(进阶)：`.setStyle()` 方法——让通知呈现富文本

{% mermaid %}
flowchart TD
	Style --> BigPictureStyle;
	Style --> BigTextStyle
{% endmermaid %}

``` kotlin setStyle 举例
//大图片
.setStyle(NotificationCompat.BigPictureStyle().bigPicture(
		BitmapFactory.decodeResource(resources, R.drawable.big_image)))
//长文字
.setStyle( NotificationCompat.BigTextStyle().bigText(string: String) )
```

#### 点击事件

- 为通知设置点击事件：`.setContentIntent(pi: PendingIntent)`

``` kotlin 构建PendingIntent
<S> PendingIntent.getXXX(ctx: Context, 0, intent: Intent, flag: Int): PendingIntent
//para4用于确定PendingIntent的行为，一般可取0详，情查文档
//XXX可以是Activity、Broadcast、Service
```

### 调用摄像头与相册

####  From Camera

<div class="mermaid">
flowchart TB
    A[fa:fa-camera-retro intent机制呼出相机拍照]
	获取对应的Uri对象 --> 给intent传入Uri指定相片将写入的文件
	A --> 将拍摄的照片显示出来
	创建对应路径的File对象 --> 获取对应的Uri对象
	给intent传入Uri指定相片将写入的文件 --> A
</div>

``` kotlin 相机实例
class MainActivity : AppCompatActivity() {
    lateinit var imageUri: Uri
    lateinit var outputImage: File
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        //step1
        outputImage = File(externalCacheDir, "output_image.jpg")
        if (outputImage.exists()) {
            outputImage.delete()
        }
        outputImage.createNewFile()
        
        //step2
        imageUri = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            FileProvider.getUriForFile(this, "com.example.cameraalbumtest.fileprovider", outputImage)
        } else {
            Uri.fromFile(outputImage)
        }//从Android 7.0系统开始，直接使用本地真实路径的Uri被认为是不安全的，会抛出一个FileUriExposedException异常。
        //而FileProvider则是一种特殊的ContentProvider，它可以选择性地将封装过的Uri共享给外部，从而提高了应用的安全性。

        //step3~4
        val intent = Intent("android.media.action.IMAGE_CAPTURE")
        intent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri)
        startActivityForResult(intent, takePhoto)
    }

    //step5
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        when (requestCode) {
            takePhoto -> {
                if (resultCode == Activity.RESULT_OK) {val bitmap = BitmapFactory.decodeStream(contentResolver.openInputStream(imageUri))
                    imageView.setImageBitmap( bitmap)
                }
            }
        }
    }
    ...
}

```

- 代码中用到`"com.example.cameraalbumtest.fileprovider"`，需要在Manifest中注册。

``` xml fileprovider注册示例
<provider
    android:name="androidx.core.content.FileProvider"	//固定不变
    android:authorities="com.example.cameraalbumtest.fileprovider"	//与FileProvider.getUriForFile()第二个参数一致
    android:exported="false"	//是否允许外部使用
    android:grantUriPermissions="true">
    <meta-data	//指定Uri共享路径
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
</provider>
```

``` xml res/xml/file_paths.xml文件示例
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="my_images" path="/" />
    //                   随意       指定共享目录
</paths>
```

#### From Album

<div class="mermaid">
flowchart LR
	发送请求Intent-->接受并处理结果Result
</div>
``` kotlin 相簿示例
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        //*step1
        val intent = Intent(Intent.ACTION_OPEN_DOCUMENT)
        intent.addCategory(Intent.CATEGORY_OPENABLE) 
        intent.type = "image/*"
        
        startActivityForResult(intent, fromAlbum)
    }

    //step2
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        when (requestCode) {
            fromAlbum -> {
                if (resultCode == Activity.RESULT_OK && data != null) {
                    data.data?.let { uri ->
                        val bitmap = getBitmapFromUri(uri)
                        imageView.setImageBitmap(bitmap)
                    }
                }
            }
        }
    }

```

### 播放多媒体文件

#### 音频概览

<div class="mermaid">
classDiagram
class Activity {
	getAssets();
}
class AssetManager {
	+openFd();
}
class MediaPlayer {
	+setDataSource();
	+prepare();
}
Activity-->AssetManager: 创建
AssetManager-->MediaPlayer: 提供播放资源
</div>

#### MediaPlayer 类

> MediaPlayer 可以用于播放**网络、本地、app安装包中的音频**

| **方法名**      | **功能描述**                                        |
| --------------- | --------------------------------------------------- |
| setDataSource() | 设置要播放的音频文件的位置                          |
| prepare()       | 在开始播放之前调用，以完成准备工作                  |
| start()         | 开始或继续播放音频                                  |
| pause()         | 暂停播放音频                                        |
| reset()         | 将MediaPlayer对象重置到刚刚创建的状态               |
| seekTo()        | 从指定的位置开始播放音频                            |
| stop()          | 停止播放音频。调用后的MediaPlayer对象无法再播放音频 |
| release()       | 释放与MediaPlayer对象相关的资源                     |
| isPlaying()     | 判断当前MediaPlayer是否正在播放音频                 |
| getDuration()   | 获取载入的音频文件的时长                            |

#### AssetManager 类

> `app/src/main/assets` 内的文件和子目录在项目打包时会一并打包到安装文件中。
>
> `AssetManager` 就是用来管理`asset`内文件的工具类

#### 音频示例

``` kotlin 播放安装包内音频
class MainActivity : AppCompatActivity() {

    private val mediaPlayer = MediaPlayer()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        initMediaPlayer()
        play.setOnClickListener {
            if (!mediaPlayer.isPlaying) {
                mediaPlayer.start() // 开始播放
            }
        }
        pause.setOnClickListener {
            if (mediaPlayer.isPlaying) {
                mediaPlayer.pause() // 暂停播放
            }
        }
        stop.setOnClickListener {
            if (mediaPlayer.isPlaying) {
                mediaPlayer.reset() // 停止播放
                initMediaPlayer()
            }
        }
    }

    private fun initMediaPlayer() {
        val assetManager = assets
        val fd = assetManager.openFd("music.mp3")
        mediaPlayer.setDataSource(fd.fileDescriptor, fd.startOffset, fd.length) 
        mediaPlayer.prepare()
    }

    override fun onDestroy() {
        super.onDestroy()
        mediaPlayer.stop()
        mediaPlayer.release()
    }

}
```

#### 视频概论

VideoView 类实际上是在MediaPlayer上的一层封装。

注意VideoView不支持播放asset文件，但可以播放`app/src/main/res/raw`下的文件（想到VideoView是一种View就不难理解）。

注意VideoView不适合做功能强大的视频播放器。

#### 视频实例

``` xml VideoView控件
<VideoView
           android:id="@id+videoView"
           ...layout_width=...
           ...layout_height=...
           ... />
```

``` kotlin VideoView类
/*class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)*/
        
        val uri = Uri.parse("android.resource://$packageName/${R.raw.video}")
		//留意资源URI字符串格式
        videoView.setVideoURI(uri)
        
        /*
        play.setOnClickListener {
            if (!videoView.isPlaying) {
                videoView.start() // 开始播放
            }
        }
        pause.setOnClickListener {
            if (videoView.isPlaying) {
                videoView.pause() // 暂停播放
            }
        }
        replay.setOnClickListener {
            if (videoView.isPlaying) {
                videoView.resume() // 重新播放
            }
        }
    }*/

    override fun onDestroy() {
        super.onDestroy()
        videoView.suspend()//释放VideoView占用的资源
    }
}
```

## 后台默默劳动者 Service

### 安卓多线程

#### Kotlin 多线程编程

``` kotlin kotlin创建线程的三种方式
//1. 继承重写Thread()
class MyThread: Thread() {
    override fun run() {...}
}
MyThread.start()

//2-1. 用接口构造Thread()
Thread(object: Runnable {	//注意这里是匿名内部类的写法
    override fun run() {...}
}).start()

//2-2. kotlin单抽象方法接口の简化版
Thread{/*Lambda*/}.start()

//3. 用kotlin内置顶层函数
thread {
    //logic
}
```

#### 异步消息处理机制

> Android 只能在主线程中改UI，因此在子线程中想要进行UI操作就需要异步机制

<img src="https://gitee.com/BAIDI-CODER/PicGo/raw/master/img/20200916163550.png" alt="图10-1 Android 异步消息处理机制" style="zoom:90%;" />

- Message是在线程之间传递的消息，它可以在内部携带少量的信息，用于在不同线程之间传递数据。
- Handler主要用于发送和处理消息，发送消息一般是使用Handler的sendMessage()方法、post()方法等，而发出的消息经过一系列地辗转处理后，最终会传递到Handler的handleMessage()方法中。
- MessageQueue是消息队列的意思，它主要用于存放所有通过Handler发送的消息。这部分消息会一直存在于消息队列中，等待被处理。每个线程中只会有一个MessageQueue对象。
- Looper是每个线程中的MessageQueue的管家，调用Looper的loop()方法后，就会进入一个无限循环当中，然后每当发现MessageQueue中存在一条消息时，就会将它取出，并传递到Handler的handleMessage()方法中。每个线程中只会有一个Looper对象。

```kotlin 异步消息处理机制实例
class MainActivity : AppCompatActivity() {

    val updateText = 1

    //在主线程中创建一个Handler对象，当接收到Message时该对象的.handleMessage()就能在主线程中被调用
    val handler = object : Handler() {
        override fun handleMessage(msg: Message) {
            // 在这里可以进行UI操作
            when (msg.what) {
                updateText -> textView.text = "Nice to meet you"
            }
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        changeTextBtn.setOnClickListener {
            thread {
                val msg = Message()
                msg.what = updateText
                handler.sendMessage(msg) // 在子线程将Message对象发送出去
            }
        }
    }
//一趟下来Message从子线程辗转到了主线程
}
```
<div class="mermaid">
classDiagram
class Message {
	Int what
	Int arg1
	Int arg2
	Object obj
}
class Handler {
	+sendMessage(Message m)
	+post()
	+handleMessage(Message m)*
}
Handler --> Message: 发送
Message --> Handler: 处理
</div>

#### AsyncTask 类

- 重写四个方法，`.execute()`启动

``` kotlin 解析AsyncTask类
<A> AsyncTask<paramsType, progressType, resultType> {
//注意三个泛型：参数类型      进度类型        结果类型
    <A> onPreExecute()	//后台开启前的初始化任务
    <A> doInBackground(paramsType...)	//后台任务：在子线程中运行
    <A> onProgressUpdate(progressType...)	//后台publishProgress(progressType...)后执行（通常在这改UI）
    <A> onPostExecute(resultType)	//后台return后执行
    <+> execute()	//启动任务
}
```

<img src="https://gitee.com/BAIDI-CODER/PicGo/raw/master/img/20200916174433.png" alt="图10-2 AsyncTask抽象方法の关系" style="zoom:80%;" />



### Service 基础

#### Service 类

``` kotlin Service类解析
class Service {
    onCreate()	//创建时调用（第一次<Activity>.startService, bindService）
    onStartCommand(intent: Intent, flags: Int, startId: Int)	//启动时调用(startServi...)
    onDestroy()	//销毁时调用(stopService, unbindService)
    onBind(): IBinder //返回一个绑定接口便于Activity与Service交互
}
```

#### 创建、启动与销毁

``` kotlin Activity中启动与停止Service
        startServiceBtn.setOnClickListener {
            val intent = Intent(this, MyService::class.java)
            startService(intent) // 启动Service，会同时调用<Service>.onCreate和onStartCommand
        }
        stopServiceBtn.setOnClickListener {
            val intent = Intent(this, MyService::class.java)
            stopService(intent) // 停止Service

```

#### Binder 类

继承一个Binder，在类中编写自己的函数，通过onBind() 返回该实例给Activity，Activity中调用该实例的方法以实现Activity与Service的通信

<div class="mermaid">
classDiagram
class IBinder
&lt&ltinterface&gt&gt IBinder
class MyBinder {
	&lt&lt自定义&gt&gt
}
IBinder <|-- Binder
Binder <|-- MyBinder
</div>

#### ServiceConnection 类

<div class="mermaid">
classDiagram
class ServiceConnection {
	onServiceConnected(ComponentName name, IBinder service)
	onServiceDisconnected(ComponentName name)
}
</div>

#### 绑定

``` kotlin 绑定示例
class MainActivity : AppCompatActivity() {
    /*
    lateinit var downloadBinder: MyService.DownloadBinder

    private val connection = object : ServiceConnection {

        override fun onServiceConnected(name: ComponentName, service: IBinder) {
            downloadBinder = service as MyService.DownloadBinder
            downloadBinder.startDownload()
            downloadBinder.getProgress()
        }

        override fun onServiceDisconnected(name: ComponentName) {
        }

    }*/

    override fun onCreate(savedInstanceState: Bundle?) {
        …
        bindServiceBtn.setOnClickListener {
            val intent = Intent(this, MyService::class.java)
            bindService(intent, connection, Context.BIND_AUTO_CREATE) // 绑定Service
        }
        unbindServiceBtn.setOnClickListener {
            unbindService(connection) // 解绑Service
        }
    }
}
```

#### Service 生命周期

<div class="mermaid">
flowchart TB
调用了n次startService -->|停止服务| 1次stopService
既调用了startService又调用了bindService -->|停止服务| stopService+unbindService
A(同一个Service类只会存在一个实例)
</div>


#### 前台服务 Foreground

> 前台Service和普通Service最大的区别就在于，它会一直有一个正在运行的图标在系统的状态栏显示，下拉状态栏后可以看到更加详细的信息，非常类似于通知的效果。
>
> 由于状态栏中一直有一个正在运行的图标，相当于我们的应用以另外一种形式保持在前台可见状态，所以系统不会倾向于回收前台Service。

启用方式：在`<Service>.onCreat()`中创建一条通知，大体上与1.1中创建通知的方式一样，不同的是不用通知管理器发送通知而是以startForeground(...)发送通知。

``` xml 应用开启前台服务权限
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.example.servicetest">
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
    …
</manifest>
```



``` kotlin 前台服务实例
class MyService : Service() {
    …
    override fun onCreate() {
        super.onCreate()
        val manager = getSystemService(Context.NOTIFICATION_SERVICE) asNotificationManager
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel("my_service", "前台Service通知", NotificationManager.IMPORTANCE_DEFAULT)
            manager.createNotificationChannel(channel)
        }
        val intent = Intent(this, MainActivity::class.java)
        val pi = PendingIntent.getActivity(this, 0, intent, 0)
        val notification = NotificationCompat.Builder(this, "my_service")
            .setContentTitle("This is content title")
            .setContentText("This is content text")
            .setSmallIcon(R.drawable.small_icon)
            .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.large_icon))
            .setContentIntent(pi)
            .build()
        startForeground(1, notification)
    }
    …
}
```

#### IntentService 类

<div class="mermaid">
classDiagram
class IntentService {
	&lt&ltAbstract&gt&gt
	onHandleIntent(Intent intent)*
}
class MyIntentService {
	&lt&lt自定义&gt&gt
}
class Service {
	&lt&ltAbstract&gt&gt
}
Service <|-- IntentService
IntentService <|-- MyIntentService
</div>

- 作用：自动开启线程、自动停止服务

