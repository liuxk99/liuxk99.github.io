---
layout: post
title:  "用Jekyll搭建本地写作环境"
date:   2020-08-12T14:27:11+08:00
catalog:  true
tags:
    - Jekyll
---

## 参考
[搭建Jekyll本地写作环境@Gityuan](http://gityuan.com/2015/06/07/build-jekyll/)

## 一、缘起
作为一个开发者，写博客是一个很好的习惯，但是这个事情并不轻松。笔者之前在[CSDN](https://blog.csdn.net/liuxk99)上写过好多年，缺点是代码格式，图文排版的问题需要自己解决，此外广告也比较多，影响到阅读体验。在过去的许多年过中，有些所得，仅在有道笔记之类的软件中保存，直到近期重新有了写作的想法。平时在笔记中也采用markdown格式来记录，它的好处是专注于内容的书写，格式排版相对容易，于是开始探索用markdown来写博客的方案。巧的是发现了gityuan.com就是基于markdown建立起来的，而且可以直接在github pages上建站，正中下怀！<br>
于是参考了[yuanhuihui.github.io](https://github.com/yuanhuihui/yuanhuihui.github.io) 的主题风格，以及[搭建Jekyll本地写作环境@Gityuan](http://gityuan.com/2015/06/07/build-jekyll/) 创建本地写作环境。在安装和配置的过程中遇到了一些问题，比如ruby的版本, gem源也变化了。基于当前的base，有必要记录一下这个过程。<br>
`注`：读者也需要关注一下这些软件的版本及其依赖，如果正好你使用相同的环境，那么笔者希望按照这个步骤可以轻松的搭建出来。如果是基于其它的OS，可能需要自己再解决一下问题。

## 二、简介
Jekyll是一个开源的博客生成工具，类似WordPress。但与之不同的是，jekyll只生成静态网页，并不需要数据库支持。
通常配合第三方评论系统使用，例如Disqus, 最关键的是jekyll可以免费部署在Github上，而且可以绑定自己的域名。
Jekyll依赖于ruby, msys2。

## 三、版本

|Name|Version|
|---|
|OS|Windows 10
|ruby-installer |Ruby+Devkit 2.6.6-1 (x64)
|ruby |ruby 2.6.6p146 (2020-03-31 revision 67876) [x64-mingw32]
|jekyll |jekyll 4.1.1

## 四、安装Ruby

Jekyll是用ruby语言编写的，所以笔者们首先要在装好ruby环境，下面是Windows环境的步骤。

### Window环境
1.  下载[RubyInstaller](http://rubyinstaller.org/downloads/)，选择含Devkit的版本。笔者选择的是 `Ruby+Devkit 2.6.6-1 (x64)`

        选择合适版本，注意操作系统是否64位版本。

2.  安装Ruby。

        记得要勾选 Add Ruby executables to your PATH，其作用是绑定ruby环境变量，另外安装目录不可以包含空格。

3.  安装MSYS2和MINGW。日志[MSYS2.install](/files/MSYS2.install.log)
![MSYS2](/img/MSYS2.png)
		
		MSYS2 and MINGW development toolchain
---

##  五、安装Jekyll

对于Window则打开CMD窗口。

### 1. 更换源

    *  无翻墙软件，可使用国内淘宝提供的源
	gem sources --remove https://rubygems.org/
	gem sources -a https://gems.ruby-china.com/
	gem sources -l

    * 有翻墙软件，可以使用如下源
	gem sources --remove https://rubygems.org/
	gem sources -a  http://rubygems.org/

日志：[gem-sources](/files/gem-sources.log)，随着时间流逝有些源可能失效，请读者自行解决。

### 2.  安装jekyll

	gem install jekyll

开始安装，因为是联网安装，所以可能时间比较常，耐心等待，至此Jekyll 安装全部完成。日志：[gem.install.jekyll](/files/gem.install.jekyll.02.log)


### 3. 安装paginate
    gem install jekyll-paginate

并在_config.yml 中加入一句 gems: [jekyll-paginate]，日志：[gem.install.jekyll-paginate](/files/gem.install.jekyll-paginate.log)

---

## 六、使用jekyll

*  先把github博客Clone下来
```
git clone https://github.com/[username]/[username].github.io.git
git clone git@github.com:[username]/[username].github.io.git
```
clone有两种方法，第一种是https方法，通过直接输入账号密码的格式提交代码；第二种是ssh的方式，需要提前配置SSH，之后可直接push代码。

* 启动jekyll服务
```
cd xxxx.github.io.git
jekyll serve --watch
```

*  浏览：[http://localhost:4000/index.html](http://localhost:4000/index.html)
*  日志：[jekyll.serve--watch](/files/jekyll.serve--watch.log)

**注意事项：**在md开头部分，date可以使用iso格式，包括时区信息就不会出错了。
```
date:   2020-08-12T14:27:11+08:00
```
具体的时间，可以通过下面命令获得：
```
$ date -Is
2020-08-12T20:03:51+08:00
```

高亮： <https://highlightjs.org/static/demo/>

## 七、提交文章
除了MSYS2，也可以使用cygwin来访问github。

### 1. 配置git用户名和邮箱

    $ git config --global user.name "{username}"     //用户名替换{username}
    $ git config --global user.email "{email}"    //邮箱替换{email}

### 2. 配置SSH

    $ ssh-keygen -t rsa -C"{email}"    //邮箱替换{email}

一路回车到命令完成，win7系统默认在文件夹`C:\Users\{你的用户名}\.ssh` ，该文件夹有`id_rsa`（私钥） 和 `id_rsa.pub`（公钥） 两个文件。

将`id_rsa.pub`内容复制到自己的Github主页的`Settings -> SSH keys`，添加完毕即可。

### 3. 提交

    $ cd {username.github.io}
    $ git add .
    $ git commit -m "提交简介"
    $ git push origin master

## 八、后记
随着时间流逝有些源可能失效，ruby, MSYS2, jekyll 的版本也必然会变化，如果遇到问题，可以对比一下实际执行和文章中的日志内容的差别，可能会发现一些线索。