---
categories:
  - 技术
  - 语言
abbrlink: f1e4bda1
date: 2019-9-1 19:00:00
tags:
  - C/C++
---

## 首先看看函数指针，函数名，以及它们分别与*和&结合后的值

 - 程序清单


```c
#include<stdio.h>
void func(void){puts("成功调用");};
int main()
{
 void(*p_func)(void)=func;
 puts("func是函数名，p_func是指向该函数的指针，那么有:");
 printf("func=%p\n*func=%p\n&func=%p\
 \np_func=%p\n*p_func=%p\
 \n&p_func=%p",func,*func,&func,p_func,\
 *p_func,&p_func);
 
 //puts("\n接着我们尝试用上述变量调用函数:");
 //func();
 //(*func)();
 //(&func)();
 //(p_func)();
 //(*p_func)();
 return 0;
}
```

 - 执行结果<!-- more -->


```c
func是函数名，p_func是指向该函数的指针，那么有:
func=004015C0
*func=004015C0
&func=004015C0
p_func=004015C0
*p_func=004015C0
&p_func=0061FECC
```
**可见==func==,==*func==,==&func==,==p_func==,==*p_func==代表的值都是func()函数的地址** （ *&p_func是指针的地址* ）
## 再来看看将它们分别作为函数名的结果
- 程序清单

```c
#include<stdio.h>
void func(void){puts("成功调用");};
int main()
{
 void(*p_func)(void)=func;
 puts("func是函数名，p_func是指向该函数的指针，那么有:");
 printf("func=%p\n*func=%p\n&func=%p\
 \np_func=%p\n*p_func=%p\
 \n&p_func=%p",func,*func,&func,p_func,\
 *p_func,&p_func);
 
 puts("\n接着我们尝试用上述变量调用函数:");
 func();
 (*func)();
 (&func)();
 (p_func)();
 (*p_func)();
 return 0;
}
```

 ==func==，==p_func==，==*p_func==，==*func==，==&func==作函数名
 - 源程序


```c
 puts("\n接着我们尝试用上述变量调用函数:");
 func();
 (*func)();
 (&func)();
 (p_func)();
 (*p_func)();
 return 0;
}
```
- 编译运行结果

```c
func是函数名，p_func是指向该函数的指针，那么有:
func=004015C0
*func=004015C0
&func=004015C0
p_func=004015C0
*p_func=004015C0
&p_func=0061FECC
接着我们尝试用上述变量调用函数:
成功调用
成功调用
成功调用
成功调用
成功调用
```

>  可见==func==，==p_func==，==*p_func==，==*func==，==&func==作函数名均可以实现函数调用
