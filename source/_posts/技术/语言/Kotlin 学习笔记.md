---
categories:
  - 技术
  - 语言
tags: Kotlin
widgets: null
abbrlink: ec3dabc7
widget: null
---
- Kotlin 可以编译成Java字节码，也可以编译成 JavaScript，方便在没有 JVM 的设备上运行。
- kotlin源文件不需要相匹配的目录和包，源文件可以放在任何文件目录。
- \$a 和 \${a.func()}我是真没话说。
- 完善的NULL检查机制 "!!, ?, ?:"（当一个引用可能为 null 值时, 对应的类型声明必须明确地标记为可为 null。方法是： “Type后 + ？”）
- 玩转for循环：可以方便的指定遍历的步长，范围。（ .., step, downTo, in, until)
- Kotlin 的基本数值类型包括 Byte、Short、Int、Long、Float、Double 等。**不同于 Java 的是，字符==不属于==数值类型**，是一个独立的数据类型。
- 支持二进制字面量，数值类型混入下划线增加语义性。
- 有些时候较小类型不会隐式转换成较大类型，需要手动调用 .toXXX() 来进行显示的类型转换。
- 和 *golang* 一样少了分号（这就是语法糖吗i了i了
- if-else 语句与when语句等可以视为 ==表达式==（可赋值
- when 与 -> 符号构成强大的分支控制。
- 完全抛弃*基本数据类型*改用*对象数据类型*“。
- "fun xxx() = xxx" 这个语法糖结合 if else, when 等特性可以打出漂亮的组合拳。
- class 继承的时候对继承的父类后面*一般*要加括号（因为 java 继承中必调用父类构造函数）且此构造函数必调用著构造函数。

<!-- more -->

- Java 与 Kotlin 访问修饰符对照表

| 修饰符    | Java                               | Kotlin                   |
| --------- | ---------------------------------- | ------------------------ |
| pubilc    | 所有类可见                         | 所有类可见（默认修饰符） |
| private   | 当前类可见                         | 当前类可见               |
| protected | 当前类，子类，同意包路径下的类可见 | 当前类，子类可见         |
| default   | 同一包目录下的类可见（默认修饰符） | 无                       |
| internal  | 无                                 | 同一模块下的类可见       |

- 语法糖: 原生支持数据类、单例类 （自动完成 toString(), hashCode(), equals() 等方法）

  - 数据类对比

``` java Java-example
    public class Cellphone {
        String brand;
        double price;
    
        public Cellphone(String brand, double price) {
            this.brand = brand;
            this.price = price;
        }
    
        @Override
        public boolean equals(Object obj) {
            if (obj instanceof Cellphone) {
                Cellphone other = (Cellphone) obj;
                return other.brand.equals(brand) && other.price == price;
            }
            return false;
        }
    
        @Override
        public int hashCode() {
            return brand.hashCode() + (int) price;
        }
    
        @Override
        public String toString() {
            return "Cellphone(brand=" + brand + ", price=" + price + ")";
        }
    }
```


``` kotlin Kotlin-example
    data class Cellphone(val brand: String, val price: Double)	//加一个data即可
```
  - 单例类对比
```java Java-example
    public class Singleton {
        private static Singleton instance;
    
        private Singleton() {}
    
        public synchronized static Singleton getInstance() {
            if (instance == null) {
                instance = new Singleton();
            }
            return instance;
        }
    
        public void singletonTest() {
            System.out.println("singletonTest is called.");
        }
    }
```

```kotlin Kotlin-example
    object Singleton {	//只需要用object代替class即可
        fun singletonTest() {
            println("singletonTest is called.")
        }	//这里相当于一个静态方法
    }
```

- Kotlin class 类


| 语言   | 实例的Class对象   | 类的Class对象    |
| ------ | ----------------- | ---------------- |
| Kotlin | javaclass         | 类名::class.java |
| Java   | (this).getclass() | 类名.class       |

- Kotlin 标准函数

  - 一系列定义于 Standard.kt 里的函数

  - 参照表

| 函数      | 特点                                             | 代码段                            |
| --------- | ------------------------------------------------ | --------------------------------- |
| `let()`   | 调用let的对象作为Lamda的**参数**，返回最后一行   | `obj.let {(obj2 ->) //obj2是obj}` |
| `with()`  | 传入对象作为Lambda的上下文，返回最后一行         | `with(obj) {//obj上下文}`         |
| `run()`   | 调用run的对象作为Lamda的上下文，返回最后一行     | `obj.run {//obj上下文}`           |
| `apply()` | 调用apply的对象作为Lamda的上下文，**返回原对象** | `obj.apply {//obj上下文}`         |

- Kotlin “静态方法调用”
  - 单例类 `objetc {}`
  - 伴生类 `companion object {}`
  - 顶层方法 `fun xxx()`，可以被任意Kotlin代码直接调用，*文件名作为类还可以用于java静态方法调用*
  - `@JvmStatic` 注释，*只能加注在单例类方法或伴生类方法上，使其编译成java静态方法*

