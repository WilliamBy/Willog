---
categories:
  - 技术
  - 笔记
abbrlink: 37a431d8
tags:
  - Android
widgets: null
toc: true
excerpt: "《第一行代码 Android 3rd》个人笔记，归纳了该书中的主要知识点。"
thumbnail: 'https://raw.githubusercontent.com/BAIDI-coder/PicGo/master/img/wallhaven-nrmgzm.jpg'
---
# 第一行代码 Android（第三版）读书笔记

## 第一章 开启征程（概要）

- 项目结构与目录：page 17 - page 19
- Log 方法与 Logcat 的完美结合

## 第二章 Kotlin 语法糖



>  同《Kotlin 学习笔记》一同食用效果最佳

- 《第一行》中出现的 Kotlin 特性预览表

|        主次构造函数        |           参数默认值            |    键值对传参    | 字符串内嵌表达式 |
| :------------------------: | :-----------------------------: | :--------------: | :--------------: |
| init {}, constructor () {} | fun xxx(str: String = "") {...} | xxx(str = "...") |    "${value}"    |

|    let 函数式 API     |         Lambda 表达式          |    空指针检查     |         函数式 API          |
| :-------------------: | :----------------------------: | :---------------: | :-------------------------: |
| obj.let {obj2 -> ...} | {para -> ...}, {it.toString()} | ?   ?.   !!.   ?: | map.maxby {} //Lambda表达式 |

|         容器初始化         | 数据类 | 单例类 |                   循环遍历                   |
| :------------------------: | :----: | :----: | :------------------------------------------: |
| listOf(), mapOf(), setOf() |  data  | object | for-in, until, downTo, step, l...r, 容器遍历 |

|                 分支控制                  |       条件控制        |      "不变为先"      |      访问控制       |
| :---------------------------------------: | :-------------------: | :------------------: | :-----------------: |
| when () {... -> ...}, if-else 语句/表达式 | is (instanceof), else | val, var, open class | "public by default" |



## 第三章 Activity！所见即所得



- 创建 Activity

  - 创建和加载布局文件 layout
  - 在 AndroidManifest.xml 中注册Activity
  - 编写 Activity.kt 代码 （事件绑定，方法重写等）

- Activity 通信与切换

  - Intent 类
    - 显式切换：`Intent(上下文, 指定Activity 的 Class 类)`
    - 隐式切换：指定 `action` `category`，目标将会按照注册信息中的过滤条件响应 intent。
      - 过滤条件：`<category> <action> <data>`
    - 传参：`.putExtra(), .getStringExtra()`
    - 启动方式：`startActivity() //普通启动` `startActivityForResult() //请求结果`
  - Activity 类 Result 结果
    - 设置结果：`setResult()`
    - 取回结果：`override onActivityResult()`
  - Bundle 类
  - 团队合作时用**伴生类**“静态方法”来启动

- Activity 生命周期

  - 返回栈

  - 思维导图

![](https://raw.githubusercontent.com/BAIDI-coder/PicGo/master/img/image-20200529100001541.png)

- 保存信息
  - Bundle 类型
  - `onSaveInstanceState()`，`var savedInstanceState`
- Activity 启动模式
  - standard 无检查，直接压栈
  - singleTop 检查栈顶
  - singleTask 解栈恢复
  - singleInstance 创建新栈

## 第四章 UI！！ 门面功夫 :-)

