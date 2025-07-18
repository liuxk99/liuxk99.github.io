---
layout: post
title:  "使用git管理~/.ssh"
date:   2025-06-26 10:41:00
catalog:    true
tags:
    - 公钥、私钥、密钥对
    - ssh
    - git
    - uname
---
## 1、注意：仓库必须是Private
因为包含了私钥，所以托管的仓库必须是**Private。**

## 2、ssh 的基本使用场景
为了访问服务器，比如：github, gitlab, gitee等均支持ssh登录。具体配置如下：

### 2.1、添加公钥
是把秘钥对的公钥添加到设置SSH Keys中。
![github-ssh-keys.png](/images/github-ssh-keys.png)

### 2.2、ssh 命令行登录
1. 使用密钥对的私钥登录
```
thomas@Desk07B:~$ ssh -i ~/.ssh/personal git@github.com
PTY allocation request failed on channel 0
Hi liuxk99! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```
2. 使用~/.ssh/config
```
Host github
Hostname github.com
User git
**IdentityFile** ~/.ssh/personal
```
简化后的命令行。
```
thomas@Desk07B:~$ ssh github
PTY allocation request failed on channel 0
Hi liuxk99! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```
注：两条命令是完全等价的，但是后者要简洁很多。

### 2.3、ssh 常登录的计算机实例及Git服务器
1. 公司内网的个人主机实例，如：Desk06B, Desk07B, LeVM05等；
2. 公司内网的Git服务器，比如：athenar, gitlab, legit等git服务器；
3. 公共git服务器，如: github, gitlab, gitee等；
- 多端登录
由于工作的原因，在公司有一台主机和一个笔记本电脑，分别是 Linux 和 Windows，后者配置了 Cygwin 环境，支持使用 ssh；加上在家里也有一台主机是 macOS 系统，于是正好凑齐了三个主流 OS。方便起见，分别给他们命名为 Desk07B(HP-Ubuntu 20.04) Lap06B(Dell Latitude 3350) 和 Desk08A(Mac mini M1) 。
那问题就来了，在三个环境中都配置 ssh config，这些 密钥对、config能否复用呢？经过一番思考和尝试后，形成了一个解决方案。那笔者就具体展开说明，核心是创建一个 git 仓库来保存和设置。

## 3、方案
### 3.1、提取公共文件
+ 个人身份的密钥对，用来登录公共的git服务器；
+ 公司身份的密钥对，用来登录公司内网的主机和服务器；
+ 小结
```
thomas@Desk07B:~/.ssh$ ll personal* company*
-rw------- 1 thomas thomas 1675 Jul 17 10:29 company
-rw------- 1 thomas thomas  400 Jul 17 10:29 company.pub
-rw------- 1 thomas thomas 1679 Jul 17 10:29 personal
-rw------- 1 thomas thomas  399 Jul 17 10:29 personal.pub
```

### 3.2、使用软链接来管理多个环境中的`~/.ssh/config`，比如Cygwin, Linux 和 macOS。
使用者需要根据具体的环境来创建对应的软链接。
- 实例 Desk07B(HP-Ubuntu 20.04)
```
thomas@Desk07B:~/.ssh$ ll config*
lrwxrwxrwx 1 thomas thomas   12 Jun 26 18:24 config -> config.linux
-rw-rw-r-- 1 thomas thomas 3362 Jul 17 10:34 config.cygwin
-rw-rw-r-- 1 thomas thomas 2261 Jul 17 10:38 config.linux
-rw-rw-r-- 1 thomas thomas 2721 Jul 17 10:32 config.macOS
```
- 实例 Lap06B(Dell Latitude 3350)
```
$ ls -l config*
lrwxrwxrwx  1 liuxianke Domain Users   13 Jun 26 17:54 config -> config.cygwin
-rw-------+ 1 liuxianke Domain Users 3.3K Jul 17 10:44 config.cygwin
-rw-------+ 1 liuxianke Domain Users 2.3K Jul 17 10:44 config.linux
-rw-------+ 1 liuxianke Domain Users 2.7K Jul 17 10:44 config.macOS
```
macOS 环境下类推。

## 4、使用步骤
首次使用时，可以复制 `ignite.sh` 的内容复制到`/tmp/ignite.sh`，然后使用
```
bash /tmp/ignite.sh
```
## 5、解释
### 5.1、步骤
它实际将执行以下步骤：
1. 创建临时的`~/.ssh/private_key`， `~/.ssh/config`，包括修改perm为600；
2. clone代码仓库到`~/.ssh1`并取代`~/.ssh`；
3. 执行~/.ssh/hooks_post-checkout，以便于后续checkout后每次都自动执行；
```
cd ~/.ssh
cp hooks_post-checkout .git/hooks/post-checkout
chmod a+x .git/hooks/post-checkout
ls -l .git/hooks/post-checkout
```

1. 根据 SHELL 环境，创建软链接，对于 Cygwin 环境，命令如下:
```
$ ln -s config.cygwin config
```
4. 执行`ssh-config-test-company.sh`, `ssh-config-test-personal.sh`自动测试ssh登录。

### 5.2、测试脚本
```
thomas@Desk07B:~/.ssh$ ll ssh-config-test-*
-rwxrwxr-x 1 thomas thomas 117 Jul 17 10:36 ssh-config-test-company.sh
-rwxrwxr-x 1 thomas thomas  98 Jul 17 14:46 ssh-config-test-personal.sh
```

### 5.3、测试日志
#### 5.3.1、Linux
```
thomas@Desk07B:~/.ssh$ bash -x ssh-config-test-personal.sh 
+ ssh gitee
Hi liuxk2017(@liuxk2017)! You've successfully authenticated, but GITEE.COM does not provide shell access.
Connection to gitee.com closed.
+ ssh gitlab
PTY allocation request failed on channel 0
Welcome to GitLab, @liuxk99!
Connection to gitlab.com closed.
+ ssh github-proxy
Connection to ssh.github.com 443 port [tcp/https] succeeded!
PTY allocation request failed on channel 0
Hi liuxk99! You've successfully authenticated, but GitHub does not provide shell access.
Connection to ssh.github.com closed.

real	0m2.602s
user	0m0.022s
sys	0m0.009s
+ ssh github
PTY allocation request failed on channel 0
Hi liuxk99! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.

real	0m3.493s
user	0m0.021s
sys	0m0.004s
```

#### 5.3.2、macOS
```
➜  .ssh git:(master) ✗ time bash -x ssh-config-test-personal.sh
+ ssh gitee
Hi liuxk2017(@liuxk2017)! You've successfully authenticated, but GITEE.COM does not provide shell access.
Connection to gitee.com closed.
+ ssh gitlab
Connection closed by 127.0.0.1 port 7897
+ ssh github-proxy
Connection to ssh.github.com port 443 [tcp/https] succeeded!
PTY allocation request failed on channel 0
Hi liuxk99! You've successfully authenticated, but GitHub does not provide shell access.
Connection to ssh.github.com closed.

real	0m2.994s
user	0m0.038s
sys	0m0.009s
+ ssh github
PTY allocation request failed on channel 0
Hi liuxk99! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.

real	1m18.099s
user	0m0.044s
sys	0m0.014s
bash -x ssh-config-test-personal.sh  0.13s user 0.04s system 0% cpu 1:21.75 total
```