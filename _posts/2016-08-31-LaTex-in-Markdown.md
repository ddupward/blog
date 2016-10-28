---
title: "在Markdown中添加LaTex公式"
date:  2016-08-31 
author: Suewangyihe
categories: LaTex
---

Markdown虽然简约灵活，但是如何在Markdown中插入漂亮的Latex公式呢？你跟我说截图？行间公式我就忍了，行内公式也这么干本宝宝就不高兴了，相信你的读者，包括你自己都难以忍受那样丑陋的排版，那么我们还是尽可能让Markdown的公式也漂亮起来吧！


## 一. 在有网络的情况下写文章

### （1）使用google chart 或 forlosh 

相信已经有很多人安利过这两个服务器了，就右侧实时预览来说，确实好用，感觉上并不比MathTpye的预览延后。就个人体验来讲，推荐google chart。理由很简单，forlosh有时候反应慢的要命啊！！！我想调整一下公式的大小，等半个小时才能出现预览，google chart win！不过据说有一些复杂的公式google chart无法解析，在遇到如此繁复公式的时候，或许也是重新打开LaTex的时候了。

甩一个概率公式的例子，google chart：

```
<img src="http://chart.googleapis.com/chart?cht=tx&chl=P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}">
```

forlosh：

```
<img src="http://www.forkosh.com/mathtex.cgi?P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}">
```

效果如下：
（1）google chart:
<img src="http://chart.googleapis.com/chart?cht=tx&chl=P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}">
 (2) forlosh:
<img src="http://www.forkosh.com/mathtex.cgi?P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}">

实在不想说在敲这篇Markdown的时候forlosh依旧木有预览。

### （2）使用codecogs

同样支持实时右侧预览哦，只不过不在Markdown内编辑LaTex公式，而是在[LaTex在线公式编辑器](http://www.codecogs.com/latex/eqneditor.php/ "codecogs")中，在浏览器中输入该网址，先将Latex代码输入到编辑框中，然后复制下方的html代码到markdown中就可以了，依然是概率公式，得到的html代码如下：

```
<a href="http://www.codecogs.com/eqnedit.php?latex=P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}" title="P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}" /></a> 
```

得到的效果如下：

<a href="http://www.codecogs.com/eqnedit.php?latex=P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}" title="P(x|c)=\frac{P(c|x)\cdot P(x)}{P(x)}" /></a> 


### （3）使用MathJax

首先，这种方法不支持右侧实时预览，不过可以使用F6在网页上预览。在Markdown中添加MathJax引擎即可，以MarkdownPad2举例来说，选择Tools->Options，选择Advanced选项卡，选择HTML Head Edoitor，加入如下代码：

```
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
``` 

然后，你就可以像在LaTex中编辑公式一样，在markdown中写美美的公式啦！可以实现行间公式，行内公式等等的排版要求，唯一美中不足的就是需要F6在网页上预览效果，不过相信对于各位写过LaTex的同学来说，生成pdf可能还要更慢些。

比如在markdown输入如下代码：

```
当\\(\small w \cdot x_i +b>0 \\) 时， \\(\small y_i =-1 \\)，而当\\( \small w \cdot x_i +b<0\\) 时， \\(\small y_i =+1 \\)。因此，误分类点\\(x_i\\)到超平面\\(\small S\\)的距离是：
$$-\frac{1}{\|w\|}y_i(w \cdot x_i+b)$$

```

得到的效果如下：

当\\(\small w \cdot x_i +b>0 \\) 时， \\(\small y_i =-1 \\)，而当\\( \small w \cdot x_i +b<0\\) 时， \\(\small y_i =+1 \\)。因此，误分类点\\(x_i\\)到超平面\\(\small S\\)的距离是：
$$-\frac{1}{\|w\|}y_i(w \cdot x_i+b)$$
同学们应该已经注意到了行内公式等的书写发生了变化，因为转义字符等等的影响，书写习惯也确实要做些许的改变，这里不多赘述，详情请参见MathJax的官方文档：[MathJax TeX and LaTeX Support](http://docs.mathjax.org/en/latest/tex.html "MathJax")。

## 二. 在没有网络的情况下写文章

这并不是一个伪命题，不怕一万就怕万一。在没有网络的时候我们仍然需要MathJax的支撑。
我们想要预览公式的目的无非是想要检验公式书写的正确性，首先你需要在有网络时下载好MathJax到本地：[MathJax v2.6.1](https://github.com/mathjax/MathJax/releases "MathJax v2.6.1")，各位同学可以根据需要下载不同版本。
然后在本地解压并打开文件夹，选择MathJax-2.6.1，进入test文件夹，打开sample-dynamic.html，好了，尽情地输入公式吧，你会得到你想要的效果。

#### **Step1**：

![](http://i.imgur.com/Tboy3d5.png)

#### **Step2**：

![](http://i.imgur.com/akjb78P.png)

#### **Step3**：

![](http://i.imgur.com/RRMrrMG.png)

把代码复制到markdown中就可以了哦~有兴趣的同学可以试一试其他的文件。

## 三. 最后

向各位非数学系的读者安利一个手写公式就能得到Latex代码的地方：[webdemo](http://webdemo.myscript.com/views/math.html#/demo/equation "webdemo")，大家可以尝试一下，简直懒癌福音！
