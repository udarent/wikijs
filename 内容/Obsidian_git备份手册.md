---
title: Obsidian_git备份手册
description: 
published: 1
date: 2023-09-23T14:37:09.568Z
tags: 
editor: markdown
dateCreated: 2023-09-23T14:27:12.209Z
---

已剪辑自: [https://zhuanlan.zhihu.com/p/595478125](https://zhuanlan.zhihu.com/p/595478125)

### 原理

Obsidian git插件只是简单的执行commit/push/pull等命令，需要先在本地完成仓库的设置，再结合插件的定时任务功能来完成备份。

### [[git下载安装]]

打开网址[Git软件下载网址](https://link.zhihu.com/?target=https%3A//git-scm.com/downloads)，根据电脑操作系统和电脑配置选择对应的版本，我这里选择的是适用于Windows的64位版本。

  

![](https://pic4.zhimg.com/v2-8064e148c390b3d2c0a716ad0b2ca13b_r.jpg)

  

安装过程中除了注意一下第三方访问权限的问题，其他的一路下一步就可以。

  

![](https://pic3.zhimg.com/v2-a3edf0b826f62a8283376a6b3d457f0e_r.jpg)

  

安装完成后，在桌面按一下鼠标右键，点击出现的git bash here在弹出的窗口中就可以使用git了

  

![](https://pic3.zhimg.com/v2-e29d8dd089eb6a03b1b02dbdf483c5be_r.jpg)

  

下载安装后需要先完成git的本地配置才能正常使用。

### git本地配置

在git bash的窗口中输入下面的命令

- git config --global user.name "随便取一个用户名"  
    
- git config --global user.email "随便写一个符合格式的邮箱"  
    

例如：

- git config --global user.name "Flex"  
    
- git config --global user.email "111@qq.com"  
    

### github账号注册

[GitHub官网链接](https://link.zhihu.com/?target=https%3A//github.com/)，国内可能需要修改hosts文件或者魔法上网才可以连接，对国内用户友好一点的方案是使用[Gitee](https://link.zhihu.com/?target=https%3A//gitee.com/)，两种的使用方法差不多。

进入github官网，点击右上角的Sign up进行注册，如果你已经有github账号了，只需要点击sign in进行登录

  

![](https://pic1.zhimg.com/v2-41f7cea53888023abb0a0aa9b912fc78_r.jpg)

  

然后根据提示输入邮箱地址，密码，用户名以及人机验证完成账号的创建并登录

  

![](https://pic2.zhimg.com/v2-40cdfc8016dd9dbcceb0838738c406d1_r.jpg)

  

### 密钥生成与上传

### 生成本地密钥

使用下面的命令来生成密钥

- ssh-keygen -t rsa -C "你注册github时使用的邮箱"

生成过程中，它会询问你想要将密钥放在哪个位置下和设置密钥的密码，我都是留空，直接回车确定就可以了，默认的存放位置在用户文件下的.ssh文件夹下。

  

![](https://pic2.zhimg.com/v2-5c8e4770a9c8c8874ea0615dd10a3b7d_r.jpg)

  

打开.ssh文件夹可以看到两个文件，id_rsa是私钥，id_rsa_pub是公钥，使用记事本打开公钥文件对文本进行复制，方便后面的SSH密钥填写。

  

![](https://pic1.zhimg.com/v2-fc10cdd315cf93118a74ed96b6ece7d4_r.jpg)

  

### 上传公钥到github

通过如图所示的路径找到填写SSH密钥的位置，然后完成密钥名称和公钥的填写，最后进行保存。

  

![](https://pic2.zhimg.com/v2-b02e2bfea1ee396f3336ab29347d0781_r.jpg)

  

### 创建仓库

在github首页的左上方点击Creat repository创建仓库，按照下图所示填写仓库信息。

  

![](https://pic3.zhimg.com/v2-5aeabad386455e95ecdbf4e53fdb6c16_r.jpg)

  

### 连接仓库

在需要备份的Obsidian库中打开git bash，使用以下命令对库进行设置

- git init(初始化仓库，让你的Obsidian库变成可以通过git管理的本地仓库)  
    
- git remote add origin (使用git命令将本地Obsidian库和github上的仓库进行连接)  
    

### git三件套

在连接好仓库后，使用以下命令将Obsidian库中的文件备份到github仓库中去

- git add <文件名>(将文件添加到暂存区中，可以使用git add --all(添加库中所有文件到暂存区))  
    
- git commit -m "提交时的备注信息"(将暂存区中的文件进行提交)  
    
- git push origin master(将提交的文件推送到github仓库中区，首次推送需要使用git push -u origin master)  
    

### Obsidian git 插件安装

点击obsidian首页左下角的设置按钮，在弹出的窗口中找到第三方插件并点击，你需要做的是先关闭安全模式，然后点击社区插件的浏览按钮，在搜索框中输入Obsidian git点击安装后再点击启用就完成了。

  

![](https://pic4.zhimg.com/v2-ca6368edf02c726786dfb7c8e08254bb_r.jpg)

  

### Obsidian git 插件设置

在第三方插件中找到Obsidian git插件，然后按图示设置自动备份时间和当检测到有文件修改就自动备份

  

![](https://pic4.zhimg.com/v2-99e737a40036c49915055a4d237369c7_r.jpg)

  

### 另外一说

对Obsidian库中你不想备份的文件，例如.obsidian(Obsidian的配置文件)，.track(库的回收站文件)可以使用.gitignore文件来进行忽略，按照下图填写进文件就可以了。

  

![](https://pic4.zhimg.com/v2-700a531d01a3714e68d7f5997176fab7_r.jpg)

  

参考视频： [https://www.bilibili.com/video/BV1RF411K7aN/?spm_id_from=333.337.search-card.all.click](https://link.zhihu.com/?target=https%3A//www.bilibili.com/video/BV1RF411K7aN/%3Fspm_id_from%3D333.337.search-card.all.click)