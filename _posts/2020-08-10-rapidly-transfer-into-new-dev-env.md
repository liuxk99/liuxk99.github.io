---
layout: post
title:  "repo工程快速迁移到新的开发环境"
date:   2020-08-11T15:38:25+0800
catalog:    true
tags: 
    - repo
    - PBP
    - 开胃小菜
---
## 一、缘起
由于某种原因，需要快速的把项目代码迁移到其它开发环境中去，比如一台新的电脑。完整的步骤包括：
+ 配置repo
```Bash
repo init -u xxx -m xxx -b xxx
```
如果全新的电脑，还需要先配置repo。
+ 完整的下载代码
```Bash
repo sync
``` 
+ 补充私有文件
比如某些gradle.properties, key文件，这些文件不适合放在VCS中，但是build却又需要它们。
这些都会花费一些时间。

## 二、方案
如果已经有一个可用的开发环境，把内容打包(bundle)过去，免去下载代码，补充补充私有文件，则会降低难度，节省时间。经过实践，总结出下面的内容，可以作为一份指引，用来制作一个内容包。

## 三、步骤(PBP)
### 3.1 同步代码到最新
```Bash
time repo sync --no-tags -cj4
```
### 3.2 Build
Build(Menu)->Rebuild Project
解决build errors，确保内容完整。

### 3.3 删除生成的文件
```Bash
find -name build -type d | xargs rm -rf
```
其中build目录会生成较多文件。
```
$ find -name build -type d
./jatl/build
./TimeManagement/build
./jlibs/build
./utils4j/build
./app/build
./build
./utils4a/build
./libCalendar/build
./libTime/build
```

#### 前后对比
+ cmds
```
$ du -sh .; find -name build -type d | xargs rm -rf; du -sh .
```
+ 结果
```
 41M     .
 15M     .
```
减少了大部分内容。

### 3.4 打包
```
$ time tar cf Du-2020.08.11.tar Du/

real    0m0.740s
user    0m0.004s
sys     0m0.016s
```
#### 文件大小
```
$ ll Du-2020.08.11.tar -h
-rw-rw-r-- 1 thomas thomas 9.0M  8月 11 10:04 Du-2020.08.11.tar
```

## 四、验证
在新的开发环境下解开，如果build通过，则可以打包发布。

## 五、更新
可以根据项目的节奏，来制作项目内容包。