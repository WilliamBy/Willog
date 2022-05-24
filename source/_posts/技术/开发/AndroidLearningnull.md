---
categories:
  - 技术
  - 开发
abbrlink: fae5c3cd
---
# Android Studio 入坑笔记

- Android Studio 自动创建 resource id 失败
  - 重启 AS。
  - Sync with file system 同步一下？
  - 查看 R.java 中的 id 。Project 下依次打开app-->build-->generated-->source-->r-->debug，在debug中的两个选项的子文件中分别有有一个R文件，既我们要找的R.java文件。（或者直接shift+shift查找R.java

