---
categories:
  - 技术
  - 指南
abbrlink: 6e7bdc
tags:
  - Hexo
toc: true
widgets:
  - position: left
    type: toc
---
<!--more-->
## 基本指令

```plain 基本指令
/*npm指令*/
npm install/uninstall xxx
//执行package.json中定义的脚本
npm run server

/*hexo指令*/
hexo cl && hexo g && hexo s	//清理，生成，本地服务器
hexo d //部署
hexagon -l //查看插件
```

## 更多 Hexo 指令

<!-- more -->
{% link 传送门 https://hexo.io/zh-cn/docs/commands %}

## Front-matter
| 参数         | 描述                                                 | 默认值       |
| :----------- | :--------------------------------------------------- | :----------- |
| `layout`     | 布局                                                 |              |
| `title`      | 标题                                                 | 文章的文件名 |
| `date`       | 建立日期                                             | 文件建立日期 |
| `updated`    | 更新日期                                             | 文件更新日期 |
| `comments`   | 开启文章的评论功能                                   | true         |
| `tags`       | 标签（不适用于分页）                                 |              |
| `categories` | 分类（不适用于分页）                                 |              |
| `permalink`  | 覆盖文章网址                                         |              |
| `keywords`   | 仅用于 meta 标签和 Open Graph 的关键词（不推荐使用） |              |
| `excerpt`    | 摘要                                                 |              |
| `widget`     | icarus 主题空间                                      |              |

## 标签插件 Embed

> 增强了 markdown 的功能的一系列插件
> ***info: embed 语法不要包裹在反引号中，且冒号后面不要空格！！***
### 官方文档
{% link 官方插件文档 https://hexo.io/zh-cn/docs/tag-plugins %}
### 其他插件
- 探宝方法：hexo.io 搜索 embed
- 自己做：`<embed>`
{% iframe https://www.runoob.com/tags/tag-embed.html 100% 500 %}
- hexo-github-card
{% githubCard user:Gisonrg repo:hexo-github-card %}
- hexo-pdf
{% githubCard user:superalsrk repo:hexo-pdf %}
- hexo-tag-bilibili
{% githubCard user:Z4Tech repo:hexo-tag-bilibili %}
- hexo-tag-douban
{% githubCard user:YuyingWu repo:hexo-tag-douban %}
## hexo-tag-bootstrap 插件

### Components

- **textcolor** - Convey meaning through color with a handful of emphasis utility classes.
- **button** - Inserts a button with target links, text and specified color. ** {% btn url text (color=primary) (size=def|sm|lg) (outline|block) %}
- **label** - Inserts a label with text and specified color.
- **pill** - Inserts a label with text and specified color. ** {% badge color text %}
- **badge** - Inserts a badge with text.
- **alert** - Inserts alert messages with text and specified color.
- **card** - Add a card control ** *carddeck* - use with cards to combine multiples ** *cardgroup* - similar to carddeck
- **jumbo** - jumbotron
- **carousel** - carousel tag - each img within will create a new "slide"
- **row** - add a grid row
- **col** - add a column within a grid row
### 使用示例
{% link ！🐱‍🏍传送门🐱‍🏍 https://www.hahack.com/hexo-theme-freemind-blog/2014/03/16/tag-plugins/ %}
### 举亿个栗子
> 渲染效果因css而异
- badge
{% badge red text %}

- alert 块级 属性：waring danger success info
{% alert warning %}
Best check yo self, you're not looking too good.
{% endalert %}
{% alert danger %}
Change a few things up and try submitting again.
{% endalert %}
{% alert success %}
You successfully read this important alert message.
{% endalert %}
{% alert info %}
This alert needs your attention, but it's not super important.
{% endalert %}

- textcolor （暂无css渲染）
{% textcolor muted %}
Fusce dapibus, tellus ac cursus commodo, tortor mauris nibh.
{% endtextcolor %}
{% textcolor primary %}
Nullam id dolor id nibh ultricies vehicula ut id elit.
{% endtextcolor %}
{% textcolor success %}
Duis mollis, est non commodo luctus, nisi erat porttitor ligula.
{% endtextcolor %}
{% textcolor info %}
Maecenas sed diam eget risus varius blandit sit amet non magna.
{% endtextcolor %}
{% textcolor warning %}
Etiam porta sem malesuada magna mollis euismod.
{% endtextcolor %}
{% textcolor danger %}
Donec ullamcorper nulla non metus auctor fringilla.
{% endtextcolor %}

- 行内label 支持属性 default warning success danger primary info
{% label default %}
{% label warinng warning %}
{% label success success %}
{% label danger danger %}
{% label primary primary %}
{% label info info %}

- 行内btn 支持属性 primary success warning danger info
{% btn http://hahack.com btn %}
{% btn http://hahack.com btn primary %}
{% btn http://hahack.com btn success %}
{% btn http://hahack.com btn warning %}
{% btn http://hahack.com btn danger %}
{% btn http://hahack.com btn info %}

## 插入HTML来增强阅读体验

{% alert info %}
{% link Bulma https://bulma.io/documentation/ %} 是一个CSS框架。查看{% link Bulma https://bulma.io/documentation/ %}文档。
{% endalert %}
{% alert warning %}
CSS 框架不包含js，涉及点击事件需要自行处理！
{% endalert %}

<article class="message">
  <div class="message-header">
    <p>Note</p>
    <button class="delete" aria-label="delete"></button>
  </div>
  <div class="message-body">
    不用js的情况下适合的组件有：button，dropdown，message
  </div>
</article>

<div class="dropdown">
  <div class="dropdown-trigger">
    <button class="button" aria-haspopup="true" aria-controls="dropdown-menu3">
      <span>Click me</span>
      <span class="icon is-small">
        <i class="fas fa-angle-down" aria-hidden="true"></i>
      </span>
    </button>
  </div>
  <div class="dropdown-menu" id="dropdown-menu3" role="menu">
    <div class="dropdown-content">
      <a href="#" class="dropdown-item">
        Overview
      </a>
      <a href="#" class="dropdown-item">
        Modifiers
      </a>
      <a href="#" class="dropdown-item">
        Grid
      </a>
      <a href="#" class="dropdown-item">
        Form
      </a>
      <a href="#" class="dropdown-item">
        Elements
      </a>
      <a href="#" class="dropdown-item">
        Components
      </a>
      <a href="#" class="dropdown-item">
        Layout
      </a>
      <hr class="dropdown-divider">
      <a href="#" class="dropdown-item">
        More
      </a>
    </div>
  </div>
</div>

<div class="dropdown is-hoverable">
  <div class="dropdown-trigger">
    <button class="button" aria-haspopup="true" aria-controls="dropdown-menu4">
      <span>Hover me</span>
      <span class="icon is-small">
        <i class="fas fa-angle-down" aria-hidden="true"></i>
      </span>
    </button>
  </div>
  <div class="dropdown-menu" id="dropdown-menu4" role="menu">
    <div class="dropdown-content">
      <div class="dropdown-item">
        <p>You can insert <strong>any type of content</strong> within the dropdown menu.</p>
      </div>
    </div>
  </div>
</div>
## 博客备份迁移

{% iframe https://www.dazhuanlan.com/2020/03/18/5e7196c49acac/ 100% 500 %}