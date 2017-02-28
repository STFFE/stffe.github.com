---
layout: post
title:  "2017-02-28-在ubuntu中安装mongodb六部曲"
date:   2017-02-28
categories: DB
author: "兰悦儿格格"
---

话说，楼主以前是吃过苦的，之前我因为想学 MongoDB ，所以用我的 windows 来安装 MongoDB ，结果苦了我了，一整天都没装好。

后来看到同事用自己的 linux 系列服务器一些字就装好了，我。。。

所以奉劝大家，要么在 windows 上直接下载 MongoDB 软件比如 **mongoChef Core** ,要么，就还是用 linux 系列比如 ubuntu 来装吧。
安装某些软件，windows用户伤不起啊。

## 1. 根据包管理系统来导入公钥
 这里的包管理系统指的就是你的包管理工具是**dpkg** 还是 **apt** ,或者是其他的包管理工具.
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
```
这个公钥呢，就是酱紫
> -----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.11 (GNU/Linux)

>mQINBFYYLZUBEADTvHI/DDlJY4JCLh7chtQiKkj8kFpqOtY4x6luOQWvYNXfvso1
yoKqKnU33Fh3JY4dWClXzv40PcVH4pIi95enzCLGvU30GNDsfmueV9vkq5HrCMMZ
rQ1M9/4HgrnbRvLhvcb4VY+RELEdcHWhUkYTpG00YuEHdgJ3PoPL5pDu1L1z/MwE
TVwCr63kl2HhgzqtpaXC00hbLSN/+GD3guCrIJy8Gfz6yfgpmCxr5KyOG2fdRoZL
mRKNzMdSua+bLeTM8BXqJpSB8BBsBSbnNuCLZdZhhSm4P7SUSrPoSRRzEMDwzv6e
1JqJqVaiApBoSX+elUzoYrglSkaPqWiT6kzlWw7ZOTTqKkojQftYGOvDYdvSKq0z
e8QzA/22FmDKzurBpv7Tus9VV+yMlxTCVGtb74u8j6pRO2jxs9w6Ur2CfQJgddtN
wbiIKWI1F7//YDSJncJRfW19cnOyrOqiNoPQqsF+YFexEy4wSs94eQ3EfBzbYjSt
EtJVSV5HCjJtIZjOxRJnyN8ZZZCPwhizvU+zGcCiAi0bwcur9kti+bnIbYCTURFy
WCLHM09HNCYg5ZBCa+6+ZDiOs+Y3sjs+Dzrrt3eAh+ny53BSBS+MlZ2CtvJZdjMu
JBAN2haiMYtaVIgmnfzfPZDJ1mZag/lGy++lufSQNJDPfVqVRlj17SG6kwARAQAB
tDdNb25nb0RCIDMuMiBSZWxlYXNlIFNpZ25pbmcgS2V5IDxwYWNrYWdpbmdAbW9u
Z29kYi5jb20+iQI+BBMBAgAoBQJWGC2VAhsDBQkDwmcABgsJCAcDAgYVCAIJCgsE
FgIDAQIeAQIXgAAKCRDWj6UP6jEpJ8KPD/9ZMK86x0ZNPK6PrJ4mmc7TVYzl9/Fb
PThLIvX/f1pUcUiLha0+TC7pYw8vWtl2iiOfAWI2bzPcQ6qrxo2wEvEE27ApL9PU
hbaUA0C/3BQzVwCojKka8If1GIf8dJVpxeai7Mcsx2vf+0svvoMvFOrTK5G2AnyH
MAY0Ko4Mw/1D/gFO1DjO90S8H6yvA0hapjwYQ8Tul0/u4wBjTRd3SNaoNnx9zcqD
evAmrDjNHDAr3WXhDlYCnciHQoqI+XUgnFMA+3zY0YczSGAU1aeUUj9F6Wr5fOlN
4pvzIaI+dQL/K9lY/2GgXEATRTuHYFZxT7gl7V6Su9wNVEBqTRkpnoGpxh1OQuRv
SHIRD9GAcP9eTD4AknvfEDbe/Hvqf9gDErGNEjy31hxSjZ5gl7WOJZw0rftlw0GN
pPmKiuT54kwUrluxtAXey9JaQ2ifomZByrxczWpIXSAi8G4ljiyJVkohmotbEiXh
PAYNtxWf0y57EQ6DO1d8yGv4BiB08YtrqaH/cFpNtRTgvajJm5iW55yb4v1Inf4d
9f7j9cQmFufJMtOMiF8/yDiFeOBkSfJXjx3+of4pXLIAuMbX95RWuAa7FOvL7xrs
RDhdohAjLyyBRcJBv8yQ9BYXOekWGIiTbhUW9L1ySkpdQ8+2QOKxOgK4Yl0psMPn
YXy2T09NgATr0A==
=8Qhy
-----END PGP PUBLIC KEY BLOCK-----


## 2. 创建 MongoDB 的一个列表文件（注意，这里系统不同，安装代码也不同）
如果你的 ubuntu 系统是 **16.04** ，执行下面这个就好
```
echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
```
但是如果你的系统是 **14.04** 系列的，那就得执行这个了
```
echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
```

想了想，还是把 12 的也附上吧
```
echo "deb http://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

```
## 3. 更新本地安装包的数据源
```
sudo apt-get update
```

## 4. 安装 MongoDB 包
```
sudo apt-get install -y mongodb-org
```

这里说一句，如果你看官网的话你会发现有好几个包，那么我为什么会让大家只是安装 这个包呢。

官方对 **mongodb-org** 这个包的定义

> A metapackage that will automatically install the four component packages listed below.
这里就说了。它跟着就会自动安装其他四个包，所以你只要选择，安装这一个包就够了
另外， **/etc/mongod.conf** 配置文件会默认把你 这个包的 **bind_ip** 设置成 **127.0.0.1** 

## 5. 创建一个系统服务文件
在 /lib/systemed/system 创建一个 mongod.service 文件，文件内容如下
```
[Unit]
Description=High-performance, schema-free document-oriented database
After=network.target
Documentation=https://docs.mongodb.org/manual

[Service]
User=mongodb
Group=mongodb
ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf

[Install]
WantedBy=multi-user.target
```

## 6.运行 MongoDB 
### 开始运行
```
sudo servercie mongod start
```

### 验证 Mongodb 是否开启成功
在**/var/log/mongodb/mongod.log**查看是否有这么一句话
```
[initandlisten] waiting for connections on port <port>
```

果然在mongod.log的最新的日志信息里找到了这么一句话
```
2016-11-21T05:53:34.772-0500 I NETWORK  [initandlisten] waiting for connections on port 27017
```
### 停止 MongoDB 
```
sudo service mongod stop
```

### 重启
```
 sudo service mongod restart
```

ok。那怎么校验你是否安装成功，MongoDB 已经能用了呢？
在你的命令行里输入
```
mongo
```
这个时候当你看到类似这样的输出之后就代表已经 ok 了
```
MongoDB shell version: 3.2.9
connecting to: test
Server has startup warnings: 
2016-11-21T05:53:34.769-0500 I CONTROL  [initandlisten] 
2016-11-21T05:53:34.769-0500 I CONTROL  [initandlisten] ** WARNING: You are running in OpenVZ which can cause issues on versions of RHEL older than RHEL6.
2016-11-21T05:53:34.769-0500 I CONTROL  [initandlisten] 
```
