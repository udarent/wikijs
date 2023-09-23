---
title: wikijs-一款wiki系统
description: 
published: 1
date: 2023-09-23T14:27:31.849Z
tags: 
editor: markdown
dateCreated: 2023-09-23T14:22:42.522Z
---


已剪辑自: [https://zhuanlan.zhihu.com/p/598211449](https://zhuanlan.zhihu.com/p/598211449)

1、介绍

wikijs是一款知识共享wiki，优点是有权限管理系统、支持多人协作共同维护、支持markdown格式、支持评论、风格简洁等等。适合作为个人博客，或者小团队的文档知识库。

效果图：

![](https://pic4.zhimg.com/v2-0e84c8f608324a3b7da53ebd6a616c13_r.jpg)

23.png

  

## 2、部署流程

### 2.1、安装docker、docker-compose

基于docker安装最快速，也不污染系统。安装docker、docker-compose过程不赘述。

### 2.2、目录文件准备

目录与文件结构如下：

```
/path/to/wikijs/ # WIKIJS_HOME
    docker-compose.yml # docker-compose文件
    data/ # mysql数据目录
    initdb/ # mysql初始化文件目录
        init.sql
```

创建目录：

```
export WIKIJS_HOME=/opt/linxy/wikijs
mkdir -p ${WIKIJS_HOME}/{data,initdb}
```

创建mysql初始文件：

```
cat > ${WIKIJS_HOME}/initdb/init.sql <<EOF

-- 创建数据库
CREATE DATABASE IF NOT EXISTS `wiki`  DEFAULT CHARACTER SET utf8;

-- 授权用户
grant all privileges on *.* to 'wikijs'@'%';

EOF
```

创建`docker-compose.yml`文件：

```
cat > ${WIKIJS_HOME}/docker-compose.yml <<EOF

version: "3"
services:

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: wikijsrocks
      MYSQL_DATABASE": wiki
      MYSQL_USER: wikijs
      MYSQL_PASSWORD: wikijsrocks
    volumes:
      - ${WIKIJS_HOME}/data:/var/lib/mysql
      - ${WIKIJS_HOME}/initdb:/docker-entrypoint-initdb.d

  wiki:
    image: requarks/wiki:2.5
    depends_on:
      - mysql
    environment:
      DB_TYPE: mysql
      DB_HOST: mysql
      DB_PORT: 3306
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "80:3000"

EOF
```

### 2.3、启动

启动：

```
cd ${WIKIJS_HOME}
docker-compose up -d
```

可以通过查看wikijs日志查看启动情况：

```
docker logs wikijs-wiki-01 -f
```

## 3、基本配置

### 3.1、浏览器访问wikijs网页端

选择管理员界面，先进入后台，稍后再来创建首页：

![](https://pic4.zhimg.com/v2-680b8e1a04285a1b1e57d93add1c0e0f_r.jpg)

01.png

  

### 3.2、配置中文

- 1、下载简体中文
- 2、选择中文
- 3、点击应用

  

![](https://pic4.zhimg.com/v2-de190a1f03f7fdcb4e687affcb76141f_r.jpg)

02.png

  

### 3.3、配置常规信息

根据自己需要配置常规信息：

![](https://pic3.zhimg.com/v2-1031484486c13099417b9806309c13a2_r.jpg)

03.png

  

## 4、高级配置

### 4.1、配置网站分析工具（选配）

### 4.1.1、配置

配置百度统计：

![](https://pic3.zhimg.com/v2-e16631c8863dbf3ecb90e6049daa1fca_r.jpg)

04.png

  

关闭重链：

![](https://pic1.zhimg.com/v2-1c7aaebbe7915a903e255adef1381260_r.jpg)

05.png

  

### 4.1.2、查看效果

在访问网页时能看`hm.js?xxx`就说明配置成功：

![](https://pic3.zhimg.com/v2-9057c55abe8f8f59e35a5d5092e127ce_r.jpg)

21.png

  

### 4.2、配置邮件（选配）

配置邮件（用于发送确认邮件给使用邮件注册的用户）：

![](https://pic3.zhimg.com/v2-33eeaf734b0adcc7b4e8d178044e9f66_r.jpg)

22.png

  

### 4.3、选择github同步（推荐）

推荐选择github作为同步工具，好处就是可以双相同步：

- （wiki->git）你在wiki上改了，它会自动推送到git仓库。
- （git->wiki）你在本地写完文章，提交到git仓库，它会自动从git仓库拉取（定时，默认每5分钟同步一次），若发现新文章就同步到wiki中。

这对于我挺方便的，写完东西，直接推送到git仓库就可以了，不用再贴到wiki上，因为图片之类的附件贴到wiki感觉还是比较麻烦的（就比如这篇文章十几张图片，一张一张上传人都傻了，虽然可以批量，但git推送更一劳永逸，放在github上也兼具笔记数据安全功能。），用git推送一次就行了。

### 4.3.1、到github创建一个仓库

因为现在私有仓库也是免费的了，所以创建了私有库，记得添加一个README文件，也注意现在默认创建的分支名称是`main`。

  

![](https://pic2.zhimg.com/v2-2552f975f1de46f07ec45774eed3d43d_r.jpg)

06.png

  

### 4.3.2、在服务器上生成rsa文件

在服务器上创建rsa文件（如果已经有了可以使用现成的）：

```
ssh-keygen -t rsa
```

  

![](https://pic3.zhimg.com/v2-76552fca92451ae7f1f008752d4b4292_r.jpg)

07.png

  

### 4.3.3、github中添加部署key

把`id_rsa_wikijs.pub`的内容添加到githu中。

- 1：注意是在项目中，不是全局那边配置。

  

![](https://pic1.zhimg.com/v2-bbc7b031fb28ed04f43d4791709d3a08_r.jpg)

08.png

  

- 5：名称
- 6：将公钥内容贴到这里
- 7：允许可写
- 8：添加

  

![](https://pic4.zhimg.com/v2-6fc98717db6e9bf09b45fe8b452a26cb_r.jpg)

09.png

  

### 4.3.4、配置同步

- 4：分支选择main
- 5：证书方式选择内容
- 6：在此处填入私钥`id_rsa_wikijs`的内容

  

![](https://pic2.zhimg.com/v2-0d3a992c995a4c41c2ccc68fa03ea3a5_r.jpg)

10.png

  

- 7：配置git中的用户邮箱
- 8：配置git中的用户
- 9：不动

  

![](https://pic4.zhimg.com/v2-b0a80a256fe04a9bc72b71e417867113_r.jpg)

11.png

  

- 10：设置双向同步
- 11：默认每5分钟同步一次，可根据需要调整
- 12：导出所有未追踪文件到git仓库
- 13：强制双向同步
- 14：从git仓库中导入所有文件到wiki

  

![](https://pic3.zhimg.com/v2-5628ec3deb45e6019fb144be52d84886_r.jpg)

12.png

  

### 4.3.5、查看效果

配置成功之后，状态会变成绿色，有问题则显示红色，可根据提示信息进行修改。

  

![](https://pic2.zhimg.com/v2-07ed8d85fca73fc7db491c4f85a13799_r.jpg)

13.png

  

### 4.4、选择本地存储同步

不想那么复杂弄github双向同步，可以选择本地存储同步。本地同步只能自动单向同步，即从wiki中导出到本地文件系统（当你在wiki中修改了文件，它会导出到指定的本地文件路径），但你仍然可以手动把内容同步到wiki中（import everything）。

- 3：导出的本地文件路径
- 4：是否每天生成备份
- 5：推送到本地文件路径

  

![](https://pic2.zhimg.com/v2-8a7e494541c9e8856c395b215ffa2375_r.jpg)

14.png

  

### 4.5、配置github登录

### 4.5.1、在github中配置

- 1：在github中 Settings -> Developer settings
- 3：点击 New OAuth APP

  

![](https://pic3.zhimg.com/v2-2c90f4fc77425d8f26a7784b8e7bd8e2_r.jpg)

15.png

  

- 4：记下这里的Client ID
- 5：生成Client secrets，并记下
- 6：上传一张头像（可选）

  

![](https://pic3.zhimg.com/v2-caa28f177210e05d8b90353d22705556_r.jpg)

16.png

  

- 7：应用名称
- 8：应用主页
- 9：应用描述
- 10：登录回调

  

![](https://pic4.zhimg.com/v2-9b80c08112671e866a13381917ef9877_r.jpg)

17.png

  

### 4.5.2、在wikijs中配置

- 2：新增github身份验证
- 4：填入上面一步得到的Client ID
- 5：填入上面一步得到的Client secrets

  

![](https://pic2.zhimg.com/v2-5ff4a63a66d52ce3c55e3b7a3c2661c1_r.jpg)

18.png

  

- 6：开放注册
- 7：分配一个分组给通过github登录的账号，我这里创建了一个游客分组，拥有评论权限。
- 配置好了之后，点击右上角的应用。

  

![](https://pic2.zhimg.com/v2-243d87a706ed88d54249c2ac932ada55_r.jpg)

19.png

  

### 4.5.3、效果图

  

![](https://pic1.zhimg.com/v2-a308f33011fc4d471a45ccc39643675c_r.jpg)

20.png

  

## 5、参考