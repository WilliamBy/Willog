---
categories:
  - 技术
  - 语言
abbrlink: c737d2b3
date: 2020-3
tags:
  - 踩坑
  - Go
---
{% label Golang info %}
<!--more-->
-	"{"的位置要注意	分号无
	多变量声明：
	//类型相同多个变量, 非全局变量
- var vname1, vname2, vname3 type
- vname1, vname2, vname3 = v1, v2, v3

- var vname1, vname2, vname3 = v1, v2, v3 // 和 python 很像,不需要显示声明类型，自动推断

- vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经被声明过的，否则会导致编译错误<!-- more -->

``` go
// 这种因式分解关键字的写法一般用于声明全局变量
var (
    vname1 v_type1
    vname2 v_type2
)
```
- 默认值

- 并行特性：多个返回值，并行赋值

- 常量可以用len(), cap(), unsafe.Sizeof()函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过：
- a = a++ // 这是不允许的，会出现变异错误 syntax error: unexpected ++ at end of statement  

- ! Go 的自增，自减只能作为表达式使用，而不能用于赋值语句。

- for each range loop

- !!! 函数闭包（同一个被赋予闭包函数的函数每次调用都会将匿名函数内的局部变量记忆下来）
``` go
package main
import "fmt"
func main() {
    add_func := add(1,2)
    fmt.Println(add_func(1,1))
    fmt.Println(add_func(0,0))
    fmt.Println(add_func(2,2))
} 
// 闭包使用方法
func add(x1, x2 int) func(x3 int,x4 int)(int,int,int)  {
    i := 0
    return func(x3 int,x4 int) (int,int,int){ 
       i++
       return i,x1+x2,x3+x4
    }
}
```
- := cannot be used globally!!!!
-  全局变量和局部变量都有默认值
- 结构体与数组的匿名

- append()必须要用它的返回值（原因很简单，其参数列表用的是值传递而不是指针传递）
- map必须要初始化（用make（）或者声明的时候直接赋值），否则只是个nil，无法对其进行赋值！！！？？
- !!!!GO没有隐式类型转换！！！需要灵活使用显示类型转换！
- 将一个结构体赋值给一个接口变量可以想象成该接口成为了一个指向该结构体的“指针”
- 通道chan类型是“值相关”的，相同chan值描述同一个通道，chan用close（）关闭，）有点像file descriptor？

- 两个并发线程时间前后关系不确定，同一个程序可能有不同结果
close一个chan后还能接收，不可发送
