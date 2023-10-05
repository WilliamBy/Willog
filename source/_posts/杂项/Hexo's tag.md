---
abbrlink: 827933de
date: 2020-1-1
categories:
  - 杂项
---
{% label Hexo info %}
<!-- more -->

# Hexo 的插件扩展调试页面

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
      NotificationCompat <|-- Notification
      NotificationManager --> NotificationChannel: 创建、管理
      NotificationManager --> Notification: 发送
      Notification --* NotificationChannel: 对应
</div>
