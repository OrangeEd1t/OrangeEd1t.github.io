---

title: 使用ZerotierOne+Moon搭建高质量的远程桌面
date: 2020-08-13 19:35:14categories: 
- 内网穿透
tags:
- 教程
- Zerotier
---

# 写在前面

因为公司笔记本的TeamViewer被检测到商业行为，给我限制最多只能链接5分钟。这尼玛能忍？自己动手搞一个

# 准备材料

* 云服务器
* Zerotier账号
* 两台电脑（能上网）

# 搭建步骤

## 第一步：申请Zerotier账号

点击[Zerotier](https://www.zerotier.com/)官网

![image-20200815142113041](https://typora-img-1252867373.cos.ap-chengdu.myqcloud.com/20200815142113.png)

点击图片中的红框进行登录

![image-20200815142434568](https://typora-img-1252867373.cos.ap-chengdu.myqcloud.com/20200815142434.png)

登录成功后，点击【Networks】创建网络

![image-20200815143304685](https://typora-img-1252867373.cos.ap-chengdu.myqcloud.com/20200815143304.png)

网络创建完成后，记录下你的【Network ID】并按照上图进行设置

## 第二步：购买云服务器

请自行[百度](https://www.baidu.com/)

## 第三步：安装Zerotier服务和客户端

[Zerotier客户端下载](https://www.zerotier.com/download/)

在两台电脑上分别下载安装后客户端，启动程序

![image-20200815165928858](https://typora-img-1252867373.cos.ap-chengdu.myqcloud.com/20200815165928.png)

点击【Join Network】输入第一步中我们保存的【Network ID】

![image-20200815170356916](https://typora-img-1252867373.cos.ap-chengdu.myqcloud.com/20200815170357.png)

打开Zerotier网站，在你创建的网络中，你将会看见已加入当前网络的设备，按照上图的进行设置后，你将会在【Managed IPs】列看到设备所对应的IP地址，保存这个IP地址，该IP地址将是你以后你设备远程链接的IP地址。

至此，只要双方都加入了该网络你就可以通过IP地址远程访问了。

## 第四步：在服务器配置Moon

本来到上一步就应该结束了，无奈Zerotier的速度太慢（百度说因为服务器在国外的原因），所以本着好人做到底的原则，就提个速吧。

这里选择的是腾讯云的国内服务器，只要服务器能又公网IPV4即可。

首先，在服务器上安装Zerotier客户端

输入

```
curl -s https://install.zerotier.com/ | sudo bash
```

接着，加入网络(xxxx 是你第一步保存的 Network ID)

```
sudo zerotier-cli join xxxx
```

进入Zerotier的默认安装目录，生成moon配置文件，依次输入

```
cd /var/lib/zerotier-one
zerotier-idtool initmoon identity.public >>moon.json
```

执行完命令后会在当前目录下生成一个文件 moon.json，文件内容如下：

```
{
 "id": "0b1a7e88b0",
 "objtype": "world",
 "roots": [
  {
   "identity": "0b1c7d85b0:0:8e02a7375fc633220eaac4d4d63a14642b6d9845dc1a984506770e278a86901fa54257d554378934ab66c7e3b923b6686b1be6dc0e239efbde764aad3a6419a3",
   "stableEndpoints": []
  }
 ],
 "signingKey": "3f4d8c63cf9ed2cc075f67be5b785fda63248e134e41d3bc2bc415f850c61c48fbdc7750e2cc9910887423f660f63cb82463b7baf0613b8321005a99b4516948",
 "signingKey_SECRET": "84e4fcdc050f1bd8b43f171e7d86579b028fe32ea6e5d5775e65a09c2376df8fddfeb06b5817c43c02bb0f84a622ed06f303eb2aa5d30249bbd2406f6cbc8a31",
 "updatesMustBeSignedBy": "3f4d8c63cf9ed2cc075f67be5b785fda63248e134e41d3bc2bc415f850c61c48fbdc7750e2cc9910887423f660f63cb82463b7baf0613b8321005a99b4516948",
 "worldType": "moon"
}

```

修改【stableEndpoints】的值为公网IP+端口号的格式，类似于：

```
"stableEndpoints": ["255.255.255.88/9992"]
```

修改完成后，执行命令生成签名文件

```
zerotier-idtool genmoon moon.json
```

此命令执行完成后，会生成一个签名文件在当前目录下，文件名：0000000b1a7e88b0.moon 

签名文件生成后，就在签名文件的当前目录下新建一个文件夹（文件夹名：moons.d），然后将签名文件放在文件夹里，重启服务器即可。

## 第五步：在客户端使用配置好的Moon文件

将客户端连接上Moon节点的方法有很多，这里我就举一个比较简单的方法。

将在服务器中创建的moons.d文件夹下载到本地（包括里面生成的签名文件），然后将这个文件拷贝到Zerotier的目录下，然后重启电脑即可。

不同系统下的 Zerotier 目录位置：

```
 Windows: C:\ProgramData\ZeroTier\One
 Macintosh: /Library/Application Support/ZeroTier/One (在 Terminal 中应为 /Library/Application\ Support/ZeroTier/One)
 Linux: /var/lib/zerotier-one
 FreeBSD/OpenBSD: /var/db/zerotier-one
```

至此，所有的操作都完成了，如果需要查看是否链接上Moon节点，则输入命令`zerotier-cli listpeers`

![image-20200815182749668](https://typora-img-1252867373.cos.ap-chengdu.myqcloud.com/20200815182749.png)

如图中红框出现了你云服务器的IP地址和端口号，则表示连接成功。