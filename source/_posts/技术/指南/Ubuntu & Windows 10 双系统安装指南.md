---
categories:
  - 技术
  - 指南
tags:
  - Linux
abbrlink: 5258147c
---
{% label 技术 warning %}

{% label 系统 warning %}

{% label Linux info %}

<!-- more -->

# Ubuntu & Windows 10 双系统安装指南

## 综述

1. Linux 发行版选择

2. Ubuntu 镜像下载

3. Rufus 软件烧录镜像文件到U盘

4. **Bios 设置与 Controller Mode 修改**

5. U盘启动安装 Ubuntu

## 发行版选择

- RedHat 系
  - RedHat 商业
  - CentOS 稳定、国内用的多
- Debian 系 
  - Debian 开源
  - Ubuntu 图形界面好看、大众、易用

- 其他
  - Arch 极客至爱，萌新杀手

## 镜像下载

- 概念解读
  - LTS - 长期支持版本：*长期支持（LTS）版本通常与应用程序或操作系统有关，你会在较长的时间内获得安全、维护和（有时有）功能的更新。*
- 区别 Ubuntu Server 和 Ubuntu Desktop 版本

## 烧录镜像

> **UltraISO** 和 **Rufus** 都可以将 iso 文件烧录到U盘中

## 安装系统

- 较新的Windows10支持`shift+点击重启-》高级选项-》固件设置 `的方式进入BIOS设置
- 选择启动设备时发现没有Removable USB Device选项，只有一个Linpus Lite Device 选项，查证发现该选项对应的是我的刻录了Ubuntu的闪存U盘
- 安装双系统时在Ubuntu安装界面一定要注意给系统引导一个分区：对EFI方式的电脑要分出一个efi格式的分区作为Ubuntu系统的引导（Windows系统的引导也是这个格式，叫Windows Boot Manager），而传统BIOS方式的电脑只需要分出一个ext4的分区`/boot`。

## 知识点

### 固件

完成计算机加电后初始化等一系列工作的硬件设备

### 磁盘

磁盘基础知识、分区知识（包括分区格式MBR与GPT的区别以及对应的系统引导机制EFI/UEFI与传统Bios模式）[磁盘知识_Forskamse's Blog-CSDN博客](https://blog.csdn.net/zbgjhy88/article/details/81146756)

