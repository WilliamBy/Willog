---
categories:
  - 技术
  - 工具
abbrlink: 5a1e79ff
---
# IDEA基础功能概览：

- shortcut(edit, 代码片段, 功能), 版本控制(VCS: git, github, local history)

- Debug, javadoc generator, database, web deployment

- project&module 等概念, 模板, 项目结构查询管理

<!-- more -->

# Tips

- 代码段：main
- sout标准输出
- soutv输出变量，soutm输出方法名，soutp输出命令行参数
- 变量名.sout输出变量
- iter增强型for循环
- 引用名.for 迭代指定引用类型
- 实例名.fori顺序遍历 xxx.forr倒序遍历
- ifn --"if xxx is null", inn --"if not null"
- xxx.nn, xxx.ifn
- JavaLea
- 条件断点 --右键断点处
- ctrl+u 查看表达式的值
- Tools javadoc generator:
- Local: zh_CN; Other command line args: encoding UTF-8 -charset UTF-8
<!-- more -->
- IDE 常用快捷键对照表（原始来源: https://www.jianshu.com/p/6267a7d76018 )

| 操作                        | eclipse                           | IDEA                            | Visual Studio Code     | Sublime              |
| --------------------------- | --------------------------------- | ------------------------------- | ---------------------- | -------------------- |
| **编辑 Editing**            |                                   |                                 |                        |                      |
| 格式化代码 Format Code      | `Ctrl+Shift+F`                    | `Ctrl+Shift+L`                  | `Shift+Alt+F`          | 无                   |
| 删除行 Delete Line          | `Ctrl+D`                          | `Ctrl+Y`                        | `Ctrl+Shift+K`         | `Ctrl+Shift+K`       |
| 复制行 Copy Line            |                                   | `Ctrl+C`                        | `Ctrl+C`               | `Ctrl+C`             |
| 剪切行 Cut Line             |                                   | `Ctrl+X`                        | `Ctrl+X`               | `Ctrl+X`             |
| 重复行 Duplicate Line       | `Ctrl+Shift+Up/Down`              | `Ctrl+D`                        | `Alt+Shift+Up/Down`    | `Ctrl+Shift+D`       |
| 移动行 Move Line            | `Alt+Up/Down`                     | `Alt+Shift+Up/Down`             | `Alt+Up/Down`          | `Ctrl+Shift+Up/Down` |
| 换行 New Line               | `Shift+Enter`                     | `Shift+Enter`                   | `Shift+Enter`          | `Ctrl+Enter`         |
| 插入上行 Insert Line Above  | `Ctrl+Shift+Enter`                | `Ctrl+Alt+Enter`                | `Ctrl+Shift+Enter`     | `Ctrl+Shift+Enter`   |
| 注释 Comment                | `Ctrl+/`                          | `Ctrl+/`                        | `Ctrl+/`               | `Ctrl+/`             |
| 块注释 Block Comment        | `Ctrl+Shift+/` `Ctrl+Shift+\`     | `Ctrl+Shift+/`                  |                        | `Ctrl+Shift+/`       |
| 选择块 Block Selection      | `Alt+Shift+Up/Down`               | `Ctrl+W` `Ctrl+Shift+W`         | `Alt+Shift+Right/Left` |                      |
| 优化导包 Optimize Imports   | `Ctrl+Shift+O`                    | `Ctrl+Alt+O`                    |                        |                      |
| 代码提示 Code Assist        | `Alt+/`                           | `Ctrl+Space` `Ctrl+Shift+Space` |                        |                      |
| 代码补全 Code Completion    | `Ctrl+Alt+/`<br />比较low         | `Ctrl+Shift+Enter`              |                        |                      |
| 快速修复 Quick Fix          | `Ctrl+1`                          | `Alt+Enter`                     |                        |                      |
| Quick Access                | `Ctrl+3`                          | `Double Shift`                  |                        |                      |
| 参数信息 Parameter Info     | `Alt+Shift+/`<br />还不如 `Alt+/` | `Ctrl+P`                        |                        |                      |
| 查看API文档 Quick Document  |                                   | `Ctrl+Q`                        |                        |                      |
| 新建文件 New File           | `Alt+Shift+N` `Ctrl+N`            |                                 |                        |                      |
| 激活编辑器 Active Editor    | `F12`                             | `Esc`                           |                        |                      |
| 全部小写                    | `Ctrl+Shift+Y`                    |                                 |                        |                      |
| 全部大写                    | `Ctrl+Shift+X`                    |                                 |                        |                      |
| **导航 Navigation**         |                                   |                                 |                        |                      |
| 上/下一个Tab                | `Ctrl+PgUp/PgDn`                  | `Alt+Left/Right`                | `Ctrl+PgUp/PgDn`       |                      |
| 上/下一个                   | `Alt+Left/Right`                  | `Ctrl+Alt+Left/Right`           |                        |                      |
| 上一处修改的地方            | `Ctrl+Q`                          | `Ctrl+Shift+Backspace`          |                        |                      |
| 跳转到行 Go To Line         | `Ctrl+L`                          | `Ctrl+G`                        |                        |                      |
| 大纲/结构 Outline/Structure | `Ctrl+O`                          | `Ctrl+F12`                      |                        |                      |
| 查看类层级 Type Hierarchy   | `Ctrl+T`                          |                                 |                        |                      |
| 查看最近文件 Recent File    | `Ctrl+E` `Ctrl+F6`                | `Ctrl+E`                        |                        |                      |
| 跳到文件 Go To File         | `Ctrl+Shift+N`                    |                                 |                        |                      |
| 切换视图 Switch View        | `Ctrl+F7`                         |                                 |                        |                      |
| **重构 Refator**            |                                   |                                 |                        |                      |
| 重命名 Rename               | `Alt+Shift+R`                     | `Shift+F6`                      |                        |                      |
| 提取变量 Extract Variable   | `Alt+Shift+L`                     |                                 |                        |                      |
| 提取方法 Extract Method     | `Alt+Shift+M`                     |                                 |                        |                      |
| **搜索 Find**               |                                   |                                 |                        |                      |
| 查找下一个                  | `Ctrl+K`                          |                                 |                        |                      |
| 查找上一个                  | `Ctrl+Shfit+K`                    |                                 |                        |                      |
| 全局搜索                    | `Ctrl+H`                          |                                 |                        |                      |