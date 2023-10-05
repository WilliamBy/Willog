---
categories:
  - 技术
  - 语言
abbrlink: 9b17222d
tags:
  - Latex
---
<!--more-->

> 注意事项: \begin{tikzpicture} 环境下每一行命令结尾都要加分号;
>
> \node, \coordinate, \draw 语法参考：https://zhuanlan.zhihu.com/p/84943429

## \node 描点, \coordinate 拟点, \draw 画线, \fill 填充

- 参考网站: 
  - 路径与坐标：https://www.latexstudio.net/archives/51602.html
  - node 控制：https://www.latexstudio.net/archives/51617.html
  - 线条控制: https://www.latexstudio.net/archives/51622.html
- 示例代码:

``` latex
\node at (A)[circle,fill,inner sep=1pt]{};
%        点名          属性              
```

