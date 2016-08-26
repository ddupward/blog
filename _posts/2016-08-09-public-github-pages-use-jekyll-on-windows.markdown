---
layout: archive
title:  "Windows环境下使用Jekyll发布GitHub Pages"
date:   2016-08-09
author: hycool
categories: github page jekyll windows
---

## 前言

此文描述的是：Windows环境下使用Jekyll创建和发布GitHub Pages的建议流程。笔者使用的Windows 10操作系统，Jekyll 的版本是3.2.1，Ruby的版本是2.2.4。

整个过程分成两个大的步骤：

1. 安装Jekyll的必要依赖
2. 安装并运行Jekyll

## 第一步 : 在Windows下安装运行Jekyll的必要依赖

由于Windows平台并非Jekyll的官方支持平台，所以研发者需要另辟蹊径。好在Jeklly提供了在Windows平台下运行的解决方案。详细的官方文档请参见[windows-specific docs page](https://jekyllrb.com/docs/windows/#installation)。（PS：在我写完前，请先参见此链接的教程）

### Stpe1：安装一个名为“Chocolatey”的Windows包管理工具
请参照[Installing Chocolatey](https://chocolatey.org/install)官网所给出的安装引导。无需多虑是否将其安装在自己喜欢的目录下，请按照默认推荐的方式安装即可。简单说就是以**管理员**模式运行windows的命令行——cmd.exe。然后复制下面的命令直接执行即可。

{% highlight shell %}
@powershell -NoProfile -ExecutionPolicy Bypass -Command "[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
{% endhighlight %}

### Step2：安装Ruby
官方给出的安装方式是打开命令行（cmd.exe）直接执行：

{% highlight shell %}
choco install ruby -y
{% endhighlight %}

不过笔者执行的是，GitHub官方给出的指示是2.x.x以上均可：

{% highlight ruby %}
choco install ruby -version 2.2.4
{% endhighlight %}

### Step3：安装Jekyll（先别动，往下看）

如果你看Jekyll的官方文档，会告诉你直接打开命令行（cmd.exe）执行下面的命令：

{% highlight shell %}
gem install jekyll
{% endhighlight %}

然而，实际上第三步是非必要的，笔者建议使用[Bundler](http://bundler.io/)安装并运行Jekyll！
如果你安装第三步安装了Jekyll，然后安装官方文档的命令执行:

{% highlight shell linenos %}
jekyll build
jekyll serve
{% endhighlight %}

你会发现许多编译错误，要一个个解决并不容易。当然作为兴趣，也鼓励大家去尝试碰碰Ruby的坑。这里不再占用篇幅。

**_Bing Go!_**
<br/>

![versions of tools]({{ site.baseurl }}/assets/hy/20160812001.png)

## 第二步 : 安装并运行Jekyll

（以下步骤详情请参见[Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)）

### Step1：Clone或者Create一个代码仓库，然后创建或者检出到gh-pages分支
<span style="color:red;">**_Important!_**</span>   确保代码库根目录下有一个名为Gemfile不带后缀的文件，如果没有，请手动创建一个，这里就不在详述。
<br/>

为什么要切换到gh-pages分支？这取决于你希望将此Jekyll创建的静态资源页发布为**个人/组织主页**或者是**项目主页**。笔者这里测试的是项目主页，所以发布到gh-pages分支。

为节省篇幅不再详述有关[User, Organization, and Project Pages](https://help.github.com/articles/user-organization-and-project-pages/)的信息，请自行学习和参考。

### Step2：打开根目录下的Gemfile，手动粘贴下面两行信息：

{% highlight ruby %}
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
{% endhighlight %}

当然，如果Gemfile中已经有如上信息，则忽略此步骤。

### Step3：安装Bundle
So easy! 在Gemfile同级文件目录下执行：

{% highlight shell %}
$ bundle install
Fetching gem metadata from https://rubygems.org/............
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Resolving dependencies...
{% endhighlight %}

### Step4：生成Jekyll页面
如果你的代码库已经构建和使用过Jekyll，那么你只需运行：

{% highlight shell %}
$ bundle exec jekyll serve
{% endhighlight %}

或者，如果你想一并查看待发布的博文，可以运行：

{% highlight shell %}
$ bundle exec jekyll serve --draft
{% endhighlight %}

如果你的代码库尚未使用和创建过Jekyll静态资源页，那么请在代码仓库的Gemfile同级目录下执行

{% highlight shell linenos %}
$ bundle exec jekyll new . --force
$ bundle exec jekyll serve
{% endhighlight %}
