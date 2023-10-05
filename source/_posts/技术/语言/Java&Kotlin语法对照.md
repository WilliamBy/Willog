---
categories:
  - 技术
  - 语言
tags:
  - Java
  - Kotlin
widgets: null
abbrlink: 7a368ad4
excerpt: "比较两种语言代码的不同，方便更好的记忆与理解 Kotlin 和 Java 的关系，体会语法糖的真香。"
---
<!--more-->

## 函数式 API

- Java

  ``` java
  //传入接口类型的实例（或继承了接口的匿名类）
  /*
  	public interface Runnable {
  		void Run();
  	}
   */
  new Thread(new Runnable() {
      @Override
      public void run() {
          //执行逻辑
      }
  }).start()
  ```

- Kotlin

  ``` kotlin
  //传入Lambda表达式（单抽象的接口类型也可简化为Lambda表达式后传入）
  Thread {
      //执行逻辑
  }.start()
  ```

  

## 访问控制

| 修饰符    | Java                               | Kotlin                   |
| --------- | ---------------------------------- | ------------------------ |
| pubilc    | 所有类可见                         | 所有类可见（默认修饰符） |
| private   | 当前类可见                         | 当前类可见               |
| protected | 当前类，子类，同意包路径下的类可见 | 当前类，子类可见         |
| default   | 同一包目录下的类可见（默认修饰符） | 无                       |
| internal  | 无                                 | 同一模块下的类可见       |

## Class 对象引用

| 语言   | 实例的Class对象   | 类的Class对象    |
| ------ | ----------------- | ---------------- |
| Kotlin | javaclass         | 类名::class.java |
| Java   | (this).getclass() | 类名.class       |