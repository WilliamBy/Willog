---
categories:
  - 技术
  - 笔记
abbrlink: 37a431d8
tags:
  - Android
  - Kotlin
widgets: 
	- 
		position: left
		type: toc
toc: true
thumbnail: 'https://gitee.com/BAIDI-CODER/PicGo/raw/master/img/20200728184345.png'
date: 2020-7-28
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
      <p>"《第一行代码：Android 3rd》第一章到第五章的读书笔记，讲了AS的基本使用方法和Android项目结构，四大组件之一的Activity，以及各种UI控件的编写，还收录了本书中涉及的Kotlin的语法特性"</p>
  </div>
</article>
<!-- more -->

## 第一章 开启征程（概要）

- 项目结构与目录：page 17 - page 19
- Log 方法与 Logcat 的完美结合

## 第二章 Kotlin 语法糖

{% alert info %}
 同《Kotlin 学习笔记》一同食用更佳！
{% endalert %}

### 《第一行》中出现的 Kotlin 特性预览表

|         主次构造函数         |            参数默认值             |     键值对传参     | 字符串内嵌表达式 |
| :--------------------------: | :-------------------------------: | :----------------: | :--------------: |
| `init {}, constructor () {}` | `fun xxx(str: String = "") {...}` | `xxx(str = "...")` |   `"${value}"`   |

|        标准函数         |          Lambda 表达式           |     空指针检查      |        函数式 API        |
| :---------------------: | :------------------------------: | :-----------------: | :----------------------: |
| `let, run, apply, with` | `{para -> ...}, {it.toString()}` | `?   ?.   !!.   ?:` | `map.maxby {/*Lambda*/}` |

|          容器初始化          | 数据类 |  单例类  |                    循环遍历                    |
| :--------------------------: | :----: | :------: | :--------------------------------------------: |
| `listOf(), mapOf(), setOf()` | `data` | `object` | `for-in, until, downTo, step, l...r, `容器遍历 |

|                  分支控制                   |        条件控制         |       "不变为先"       |       访问控制        |
| :-----------------------------------------: | :---------------------: | :--------------------: | :-------------------: |
| `when () {... -> ...}, if-else` 语句/表达式 | `is (instanceof), else` | `val, var, open class` | `"public by default"` |

| 类型强转  |          repeat 函数           |    运算符重载     | 延迟初始化 |
| :-------: | :----------------------------: | :---------------: | :--------: |
| `as, as?` | `repeat(/*num*/) {/*Lambda*/}` | `operator` 关键字 | `lateinit` |

|         初始化判断          | 密封类（与when语句一同使用） |                扩展函数                | 无用参数  |
| :-------------------------: | :--------------------------: | :------------------------------------: | :-------: |
| `::($变量名).isInitialized` |        `sealed class`        | `fun ClassName.funcName(...) : ... {}` | 下划线`_` |

|                 函数类型                  |                高阶函数                |             内联函数              | 可变参数 |
| :---------------------------------------: | :------------------------------------: | :-------------------------------: | :------: |
| `ClassName.(paraType, ...) -> returnType` | 接受/返回`函数类型/Lambda表达式`的函数 | `inline` `noinline` `crossinline` | `vararg` |

|     Pair 类型     |         Smart Cast 自动转型          | 泛型  | 委托             |
| :---------------: | :----------------------------------: | ----- | ---------------- |
| `A to B`， `泛型` | `is Type` 判断后实例自动向下转换类型 | `<T>` | 类委托、属性委托 |

## 第三章 Activity！所见即所得

### 创建 Activity

- 创建和加载布局文件 layout
- 在 AndroidManifest.xml 中注册Activity
- 编写 Activity.kt 代码 （事件绑定，方法重写等）

### Activity 通信与切换

- Intent 类
  - 显式切换：`Intent(上下文, 指定Activity 的 Class 类)`
  - 隐式切换：指定 `action` `category`，目标将会按照注册信息中的过滤条件响应 intent。
    - 过滤条件：`<category> <action> <data>`	
  - 传参：`.putExtra(), .getStringExtra()`
  - 启动方式：`startActivity() //普通启动` `startActivityForResult() //请求结果` - Activity 类 Result 结果
  - 设置结果：`setResult()`
  - 取回结果：`override onActivityResult()`
  - Bundle 类
  - 团队合作时用**伴生类**“静态方法”来启动

- Activity 生命周期

  - 返回栈的概念

  - 思维导图

![Activity 生命周期](https://gitee.com/BAIDI-CODER/PicGo/raw/master/img/20200728191142.png)

- 保存信息
  - Bundle 类型
  - `onSaveInstanceState()`，`var savedInstanceState`
- Activity 启动模式
  - standard 无检查，直接压栈
  - singleTop 检查栈顶
  - singleTask 解栈恢复
  - singleInstance 创建新栈

## 第四章 UI！！ 门面功夫 :-)

### 控件 Widget
- 基本控件：TextView，EditView，Button，……
- 自定义控件：编写class（注入布局，绑定事件）$\rightarrow$  xml注册引入自定义控件 $\rightarrow$ 引入控件
- 高级一点的控件：ListVIew, RecyclerView ...
### 基本原理撇清楚

![view の 继承关系图](https://gitee.com/BAIDI-CODER/PicGo/raw/master/img/20200723232141.png)

- `Inflater` 的作用：将xml格式的布局文件解析转化成View的实例
- `findViewById()` 返回布局中指定id的View实例, 在Activity类中直接使用`findViewById()`相当于在当前Activitiy所加载的布局中寻找对应的view，而使用`view.findViewById()`则指定了寻找的区域是view内

{% alert danger %}
 留意Kotlin自动类型推导机制的使用场合，方法
{% endalert %}

## *第五章 手机平板要兼顾，探究Fragment

<div class="dropdown is-hoverable">
  <div class="dropdown-trigger">
    <button class="button" aria-haspopup="true" aria-controls="dropdown-menu4">
      <span>学习内容</span>
      <span class="icon is-small">
        <i class="fas fa-angle-down" aria-hidden="true"></i>
      </span>
    </button>
  </div>
  <div class="dropdown-menu" id="dropdown-menu4" role="menu">
    <div class="dropdown-content">
      <div class="dropdown-item">
        <p>Frag 生命周期，Frag 动态加载布局</p>
      </div>
    </div>
  </div>
</div>