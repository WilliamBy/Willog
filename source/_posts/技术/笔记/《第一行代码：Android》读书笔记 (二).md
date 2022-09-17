---
categories:
  - 技术
  - 笔记
article:
    highlight:
        theme: atom-one-light
        clipboard: true
        fold: folded
    thumbnail: true
    readtime: true
tags:
  - Android
  - Kotlin
widgets:
  - position: left
    type: toc
toc: true
thumbnail: 'https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200911100439.jpg'
date: 2020-9-11
abbrlink: c484d1a3
top: 100
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
      <p>"《第一行代码：Android 3rd》第六章到第八章的读书笔记，主要内容包括Android数据存储（持久化技术），四大组件中的Broadcast广播系统以及ContentProvider内容提供商"</p>
  </div>
</article>

<!-- more -->

> 为方便笔记，约定使用一下**伪Kotlin语法**：
>
> `<!ClassName>` 用来表示ClassName类的一个实例
>
> `<Abstract>或<A>` 写在方法(类)前用来表示该方法（类）是一个必须要给出实现的抽象方法
>
> `XXX` 表示待定的语法字符串
>
> `...` 表示前后代码段省略

## 第六章 全局大喇叭！详解广播机制

### 监听/接受广播

- 静态/动态注册BroadcastReciever，**注意静态注册的接收器不能接受隐式Intent，还缺乏Activity上下文，但是能够在没启动的情况下收听到广播信号**
- 静态注册：Manifest中注册；动态注册：Activity中注册，要记得自行销毁

### 发送自定义广播

- Intent可以不但可以穿梭于不同Activity之间，也可以穿梭于不同Application之间（Broadcast）

- 分类：**标准广播**（一传多），**有序广播**（one by one，可截断）

## 第七章 数据存储：告别不持久

{% alert warning %}

<a href="https://www.cnblogs.com/xdp-gacl/p/3634409.html" class="alert-link">Java流基础知识</a>

{% endalert %}

### 文件存储

{% alert info %}

适合存储一些简单的文本数据或二进制数据

{% endalert %}

- Context 类提供 `openFileOutput(...), openFileInput(...)`用来打开（创建）一个`/data/data/<package name>/files/`目录下的文件，方法返回一个FileOutPutStream或FileInputStream对象，该对象可使用 *Java流* 的方式IO
- 举例：FileOutPut - OutputStreamWriter - BufferedWriter

``` kotlin 文件存储实例
fun save(inputText: String) {
    try {
        val output = openFileOutput("data", Context.MODE_PRIVATE)
        val writer = BufferedWriter(OutputStreamWriter(output))
        writer.use {
            it.write(inputText)
        }
    } catch (e: IOException) {
        e.printStackTrace()
    }
}
```

### SharedPreferences 存储

<div class="alert alert-info"><i class="fa fa-info-circle  float-left"></i>  <p>SharedPreferences 是使用键值对的方式来存储数据的，支持多种数据类型的存储</p>
</div>


- 获取SharedPreferences: 
  - Context 的 `getSharedPreferences()` 
  -  Activity 的 `getPreferences()`
- 存储步骤

1. 调用SharedPreferences对象的edit()方法获取一个SharedPreferences.Editor对象。

2. 向SharedPreferences.Editor对象中添加数据，比如添加一个布尔型数据就使用putBoolean()方法，添加一个字符串则使用putString()方法，以此类推。

3. 调用apply()方法将添加的数据提交，从而完成数据存储操作。

- KTX库方法：

``` java KTX简化SharedPreferences存储
getSharedPreferences("data, Context.MODE_PRIVATE").edit {
    putString("name", "Tom")
    putInt("age", 28)
}
```

### SQLite数据库存储

{% alert info %}
SQLite 是一种轻量简便的关系型数据库，可以用来存储复杂关系的数据
{% endalert %}

- SQLiteOpenHelper帮助类，借助这个类可以非常简单地对数据库进行创建和升级（需要重写`onCreate(), onUpgrate()`）
- 两种CRUD方式：SQLiteDataBase类成员函数；SQL语句执行。
- KTX库：contentValuesOf() 方法
- 数据库文件存储路径：`/data/data/<package name>/databases/`下

#### 创建数据库

- 继承一个`SQLiteOpenHelper`的帮助类

```  kotlin  解析SQLiteHelper帮助类
//构造方案之一
SQLiteOpenHelper(context: Context, name: String, cursor: Cursor, version: Int) {
    //           上下文;            数据库名;  查询数据时用，一般传null;  版本号;
    /*抽象方法*/
    <A> override fun onCreate(db: SQLiteDatabase) {
        /*创建数据库时（name指定的数据库不存在时）调用*/
    }

    <A> override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        /*升级数据库时（version改变时）调用*/
    }
    
    /*两个重要的实例方法*/
    getReadableDatabase() getWritableDataBase() //打开（没有则创建）帮助类指定的数据库，返回一个可对数据库进行读写的对象(SQLiteDataBase)。当数据库不可写入时Readable将以只读方式打开，Writable将异常
    //注意Kotlin中可以直接调用readableDatabase、writableDataBase属性
}
```


> 可以使用AS的**Device File Explore**和**Database Navigate**插件浏览虚拟机中的文件和数据库

#### 升级数据库

- 改变`SQLiteOpenHelper`类的`version: Int`参数，则下一次打开数据库的时候（用`getReadableDatabase() ` `getWritableDataBase()`）会调用`onUpgrade()`方法。

- **最佳编写方式**：针对不同版本编写不同逻辑

``` kotlin 最佳实例
override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
    if (oldVersion <= 1) {
        db.execSQL(createCategory)
    }
    if (oldVersion <= 2) {
        db.execSQL("alter table Book add column category_id integer")
    }
}
```

#### 数据库のCRUD

- 直接执行 SQL 语句

``` kotlin execSQL()签名
<!SQLiteDataBase>.execSQL(string: String, array: StringArray)
//增删改                   sql语句（含占位符）；   参数列表；

<!SQLiteDataBase>.rawQuery(string: String, array: StringArray): Cursor
//查                     sql语句（含占位符）；   参数列表；
```

<img src="https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200910161247.png" alt="图7-1 直接执行 SQL 语句" style="zoom:67%;" />

- 使用内置方法

``` kotlin SQLiteDataBase部分方法
/*插入*/
<!SQLiteDataBase>.insert(tableName: String, null, contentValues: ContentValues)
//para2用于给某些为配值的可为空的列自动赋值为NULL；para3：ContentValue对象需要提供一系列put()方法的重载，用于向表中添加数据

//ContentValues()构建实例
val values1 = ContentValues().apply {                
    // 开始组装第一条数据                                    
    put("name", "The Da Vinci Code")                
    put("author", "Dan Brown")                      
    put("pages", 454)                               
    put("price", 16.96)                             
} 

/*修改*/
<!SQLiteDataBase>.updata(tableName: String, contentValues: ContentValues, where: String, paraArray: Array)
    //para3: 相当于sql中的WHERE部分；para4: 给WHERE中占位符分配参数的列表
   
/*删除*/
<!SQLiteDataBase>.updata(tableName: String, where: String, paraArray: Array)

/*查找*/
<!SQLiteDataBase>.query(...): Cursor 方法的参数列表见下文
/*调用query()返回一个Cursor对象*/
<!Cursor>.moveToFirst()指针移动到第一个，成功返回True
<!Cursor>.moveToNext()移动到下一个，成功返回True
<!Cursor>.getXXX(Cursor.getColumnIndex())获取根据索引取出相应类型的值（XXX为某种Type）
<!Cursor>.close()关闭指针
```

| query()方法参数 | 对应SQL部分                | 描　　述                      |
| --------------- | -------------------------- | ----------------------------- |
| table           | from  table_name           | 指定查询的表名                |
| columns         | select  column1, column2   | 指定查询的列名                |
| selection       | where  column = value      | 指定where的约束条件           |
| selectionArgs   | -                          | 为where中的占位符提供具体的值 |
| groupBy         | group  by column           | 指定需要group  by的列         |
| having          | having  column = value     | 对group  by后的结果进一步约束 |
| orderBy         | order  by column1, column2 | 指定查询结果的排序方式        |

> {% link Cursor的更多用法 https://www.jianshu.com/p/2fc0d39bd2f %}

``` kotlin KTX库简化ContentValues的组装
val values = cvOf("name" to "Game of Thrones", "author" to "George Martin", "pages" to 720, "price" to 20.85)
db.insert("Book", null, values)
```

#### SQLite 事务

- (约定db: SQLiteDataBase) 首先用`db.Transaction()` 开启事务，之后在`try`块中编写数据库事务逻辑，结束时调用`db.setTransactionSuccessful()`，如果事务处理期间抛出异常将被`catch`捕获，事务将不被处理；如果顺利完成无`Exception`抛出，则在`finally`块中调用`db.endTransaction()`结束事务。

``` kotlin 事务实例
replaceData.setOnClickListener {
            val db = dbHelper.writableDatabase
            db.beginTransaction() // 开启事务
            try {
                db.delete("Book", null, null)
//                if (true) {
//                    // 在这里手动抛出一个异常，让事务失败
//                    throw NullPointerException()
//                }
                val values = cvOf("name" to "Game of Thrones", "author" to "George Martin", "pages" to 720, "price" to 20.85)
                db.insert("Book", null, values)
                db.setTransactionSuccessful() // 事务已经执行成功
            } catch (e: Exception) {
                e.printStackTrace()
            } finally {
                db.endTransaction() // 结束事务
            }
        }
```



## 第八章 跨程序共享数据，探究 ContentProvider

### Android 权限机制

- 普通权限申请：在AndroidManifest.xml中注册即可

``` kotlin 普通权限申请
<manifest ...>
...
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
...
</manifest>
```

- 运行时权限（危险权限）申请：第一步同普通权限，第二步如下

``` kotlin 危险权限申请流程：
//检查权限是否以及授予;                          权限名（见图8-2）
if(ContextCompat.checkSelfPermission(context: Context, permissionName: String) == PackageManager.PERMISSION_GRANTED) {...}

//申请权限							                  权限列表                  申请码（标识一次申请）
ActivityCompat.requestPermissions(context: Context, permissions: StringArray, code: Int)

/*权限申请结果返回时调用*/                    /*申请码*/             /*申请的权限列表*/         /*申请结果*/
override fun onRequestPermissionsResult (requestcode: Int, permissions: Array<String>, grantResults: IntArray) {...} //申请结果要与PackageManager.PERMISSION_GRANTED比较
```

### Uri 类

- 内容URI为每一个app的ContentProvider中的数据提供了一个唯一标识符

- 内容URI字符串由协议声明、authority 和 path 组成（通配符：井号匹配任意长度数字；星号匹配任意长度字符串）

``` kotlin 内容URI字符串格式
content://com.example.app.provider/table1/1  ←这是id
协议部分   authority（包名.provider） path
```

``` kotlin 解析Uri类
//内容URI字符串解析为kotlin的Uri对象
Uri.parse(uri: String): Uri
//按‘/’分割Uri对象的内容URI的Path部分
<!Uri>.getPathSegments(): List<String>
```

<img src="https://migrate-1301429536.cos.ap-nanjing.myqcloud.com/img/20200915191206.png" alt="图8-2" style="zoom:67%;" />

### ContentResover 类

- 获取实例：通过`<!Context>.getContentResolver()` ——Kotlin中即`contentResolver`属性

- ContentResolver 的 CURD (将SQLiteDataBase对应的CURD中表名换成Uri即可)

### ContentProvider 类

> ContentProvider实例需要实现其他app访问本app的CURD接口，而ContentResolver只是提供了一个访问其他程序的api；二者CURD 函数参数列表相同。

- 创建一个ContentProvider组件
  - 使用AS的快捷方式
  - 手动注册AndroidManifest.xml
- `<ContentProvider>`组件属性
  - exported：是否允许外部程序访问
  - enabled：是否启用

- ContentProvider类中有6个抽象方法，我们在使用子类继承它的时候，需要将这6个方法全部重写。

``` kotlin ContentProvider待实现方法
<A> ContentProvider() {

    /*初始化ContentProvider的时候调用。通常会在这里完成对数据库的创建和升级等操作，返回true表示ContentProvider初始化成功，返回false则表示失败。*/
    override fun onCreate(): Boolean
    
	/*从ContentProvider中查询数据。uri参数用于确定查询哪张表，projection参数用于确定查询哪些列，selection和selectionArgs参数用于约束查询哪些行，sortOrder参数用于对结果进行排序，查询的结果存放在Cursor对象中返回。*/
    override fun query(uri: Uri, projection: Array<String>?, selection: String?, selectionArgs: Array<String>?, sortOrder: String?): Cursor?

    /*向ContentProvider中添加一条数据。uri参数用于确定要添加到的表，待添加的数据保存在values参数中。添加完成后，返回一个用于表示这条新记录的URI。*/
    override fun insert(uri: Uri, values: ContentValues?): Uri?
	
    /*更新ContentProvider中已有的数据。uri参数用于确定更新哪一张表中的数据，新数据保存在values参数中，selection和selectionArgs参数用于约束更新哪些行，受影响的行数将作为返回值返回。*/
    override fun update(uri: Uri, values: ContentValues?, selection: String?, selectionArgs: Array<String>?): Int

    /*从ContentProvider中删除数据。uri参数用于确定删除哪一张表中的数据，selection和selectionArgs参数用于约束删除哪些行，被删除的行数将作为返回值返回。*/
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<String>?): Int

    /*根据传入的内容URI返回相应的MIME类型。*/
    override fun getType(uri: Uri): String?
}
```

- UriMatcher 类：方便匹配 ContentProvider 的 CURD 方法中的Uri（当调用UriMatcher的`match()`方法时，可以将一个Uri对象传入，返回值是某个能够匹配这个Uri对象所对应的自定义代码，利用这个代码，就可以判断出调用方期望访问的是哪张表中的数据了。）

``` kotlin 解析UriMatcher帮助类
<!UriMatcher>.addURI(autority: String, path: String, code: Int)
//添加匹配规则                                         返回代码（唯一标识匹配规则）

<!UriMatcher>.match(uri: Uri): Int
//                  匹配对象    返回代码
```

- MIME 字符串格式：

  - 必须以vnd开头。

  - 如果内容URI以路径结尾，则后接`android.cursor.dir/`；如果内容URI以id结尾，则后接`android.cursor.item/`。

  - 最后接上`vnd.<authority>.<path>`。
```kotlin  MIME示例
URI:	content://com.example.app.provider/table1
MIME:	vnd.android.cursor.dir/vnd.com.example.app.provider.table1
    
URI:	content://com.example.app.provider/table1/1
MIME:	vnd.android.cursor.item/vnd.com.example.app.provider.table1
```
