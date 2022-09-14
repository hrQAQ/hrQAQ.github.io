---
title: 「hexo」mathjax公式中文渲染问题
date: 2022-09-11 08:33:27
categories: hexo
tags:
    - hexo
    - mathjax
---

在写「计算机组成原理」数制基础的时候，使用了基于mathjax的数学公式渲染器。这玩意儿居然不咋支持unicode编码，我又比较喜欢在公式里面写中文，这就出矛盾了。虽然最后在网页上的渲染结果没有出问题，但是启动hexo的时候会报一大堆错，就像这样：

```shell
SVG - Unknown character: U+8BBE in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E3A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E00 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E2A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+975E in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8D1F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6574 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6570 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5176 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FDB in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5236 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8868 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FBE in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E3A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5B58 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5728 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4EE5 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E0B in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7B49 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5F0F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8BBE in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E3A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E00 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E2A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+975E in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8D1F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6574 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6570 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5176 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FDB in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5236 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8868 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FBE in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E3A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5B58 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5728 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4EE5 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E0B in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7B49 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5F0F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+516C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5F0F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E2D in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7684 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+542B in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E49 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+662F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5C06 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5341 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FDB in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5236 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6570 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8F6C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6362 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E3A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E8C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FDB in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5236 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6570 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+662F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7684 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7F29 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5199 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+516C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5F0F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E2D in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7684 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+542B in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E49 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+662F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5C06 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5341 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FDB in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5236 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6570 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8F6C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6362 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E3A in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+4E8C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8FDB in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5236 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+6570 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0C in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+662F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7684 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7F29 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+5199 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8865 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0D in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+FF0D in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8865 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8865 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8865 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8865 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+8865 in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+53CD in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+539F in MathJax_Main,MathJax_Size1,MathJax_AMS
SVG - Unknown character: U+7801 in MathJax_Main,MathJax_Size1,MathJax_AMS
```

强迫症看了表示很不喜欢 :(    所以...我得想个办法把这个WARNING去掉。

> 为什么我知道mathjax不咋支持unicode呢？Google 报错信息关键词`U+539F`发现这是一个Unicode的字符，报错信息里面又说Unknown character了，所以大概率是mathjax不认识Unicode... 


暂且撤退TODO