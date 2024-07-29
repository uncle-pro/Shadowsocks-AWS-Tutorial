# 前言

本库主要是介绍如何使用亚马逊云服务搭建自己的Shadowsocks服务器，由于是个人服务器，一般情况下不会被封；你也可以和自己信任的人一起使用，网上Shadowsocks的教程太多而且很多已经过时，很多已经不使用不了，或者浪费了大半天时间，最后发现用不了，本教程手把手教大家从0开始，搭建一个可使用的shadowsocks服务器。

# 开始

## 注册邮箱

无论是使用亚马逊云（AWS) 还是微软云（Azure) 都需要使用邮箱， 这里推荐使用的是Gmail 或 hotmail, 当然，国内的邮箱也可以，Gmail注册相对复杂一下，这里就以hotmail 为例子示范

### 1、进入首页，

首先进入 [Outlook 首页](https://www.microsoft.com/zh-cn/microsoft-365/outlook/email-and-calendar-software-microsoft-outlook)
点击创建免费账户进行注册
![](./images/hotmail/1.png)

### 2、填为邮件名，选择 @hotmail.com
![](./images/hotmail/2.png)

### 3、输入密码
![](./images/hotmail/3.png)
### 4、输入个人信息
![](./images/hotmail/4.png)

### 5、登录邮箱，如果能登录，就是注册成功了
![](./images/hotmail/5.png)

## 注册亚马逊云（AWS) 账户

注册亚马逊云账户，亚马逊有新手12个月免费的套餐，所以如果你的邮箱是新的，没注册过的亚马逊的， 则有12个月免费的Ec2 使用期，还有100G的宽带流量（出网流量），如果是我们个人使用，一般都不会超的，足够我们日常使用了， 也就是说，我们能白嫖亚马逊12个月，当然12月后如果继续使用，会按使用量扣费的，如果只想白嫖，那请紧记要在免费期结束之前把实例给终止了。


### 1、注册并登录
1.1 进入[亚马逊云注册首页](https://signin.aws.amazon.com/signup?request_type=register)

![](./images/aws/1.png)

1.2 填入邮件地址与账户名称后，点击 [Verify email addresss] 按钮，然后会向你的邮箱发送验证码，打开的你的邮箱查看验证码，按提示录入并确认

![](./images/aws/2.png)

1.3 最后提示你的电子邮件地址验证成功， 这时要要求你创建Root 密码

![](./images/aws/3.png)

1.4 下一步，录入个人信息
![](./images/aws/4.png)
1.5 这一步，要填入账单信息，也就是要写付款的卡片信息，这里只支持外币卡，如visa或masterCard ， 国内银联是不支持的。亚马逊是后付款的，所以如果用超了免费套餐的量，就会出账单并从这卡里扣款。
![](./images/aws/5.png)

1.6 填完卡片信息后， 会要求验证电话号码， 这里填国内的电话即可
![](./images/aws/6.png)

1.7 验证完成后，最后一步，选择支持计划， 我们选择最左边的基本支持即可，点击完成注册， 因为我们是用来做shadowsocks服务的，一般情况都不需要支持服务
![](./images/aws/7.png)
![](./images/aws/8.png)
### 2、创建实例

注册完成后，我们就可以进入亚马逊的控制台了，亚马逊的全球多个地区都有服务器，所以这里你需要想好你要在哪个地区创建你的vps , 因为不同地区的政策不一样， 网速也不一起，如果你是要做跨境电商的， 那要考虑你的目标市场在哪里，最好就在你的目标市场的地区搭建vps, 如果你只是为了科学上网，那网速是你的首选，按照我的经验，在国内连接亚太地区的香港、新加坡、日本会比较快，但由于香港也被西方限制了不少， 例如GPT就限制了香港的使用，币安在日本也被限制，所以目前综合来说，新加坡是国内出墙最好的选择！


2.1 进入控制台， 选择要创建实例的地区（注意：免费的套餐是每月750个小时，也就是750/24 =31.25天，也就是说，只够开一个实例）这里选择新加坡作为创建服务器实例的地区

![](./images/aws/9.png)

2.2 进入新加坡区域后，点击EC2， 进入服务器实例配置页面
![](./images/aws/10.png)

2.3 选择实例 

![](./images/aws/11.png)

2.4 启动新实例
![](./images/aws/12.png)

2.5 进入配置页面

名称可填可不填，如果你配置多台服务器，用不同的名称好区分， 但我们如果只用一台，那不需要命名也行
主要的是下方的AMI(系统映像)， 有很多镜像是不适用的，或者是需要安装额外的插件才能用起来的。这里试了很多AMI, 发现了还是centos7镜最好用，AMI:  ami-09e2a570cb404b37e, 在搜索框输入 ami-09e2a570cb404b37e，搜索，选择即可
![](./images/aws/13.png)
![](./images/aws/14.png)


选择**Cloud Compute**

![](./images/choose_type.png)

第二步：在打开的页面选择德国Frankfurt服务器 (由于日本服务器滥用导致很多IP被封了，可以选择欧洲服务器，例如法国、德国等) ，如果喜欢其他服务器也可以选择，后续操作是一样一样的

![](./images/choose_location.png)

第三步：接下来要注意了，系统最好选择**CentOS 7 x64**，点击CentOS可以下拉选择7 x64

![](./images/choose_system.png)

第四步：选择套餐，当然ss不需要配置太高的服务器，最低配置5美金一个月的就可以了，反正我每次看2.5美金都是卖光的，如果你能看到那赶紧选啊，千年一遇。

![](./images/choose_plan.png)

记得**取消选中自动备份**(会收1美金/月)

![](./images/auto_backup.png)

第五步：接着就是部署起来了，当然你也可以给服务器起个名字再部署

![](./images/6.png)

第六步：接着等待服务器启动完成，看到Status是绿色的Running就是启动完成了，这个过程大概需要1-3分钟。

![](./images/7.png)

第七步：复制IP地址和密码，后面有用

![](./images/server_info.png)

第八步：启动完成后，当然测试一下有没有被封掉IP了，打开命令管理器或者终端，输入 ping+你的IP地址，例如我服务器IP是8.8.8.8，则ping 8.8.8.8，如果出现下图的返回信息则这个IP是可以用的，偶尔一个request timeout也是可以的，是掉包现象，如果出现一直request timeout就把这服务器删掉重新部署吧。

![](./images/10.png)

好了，到此为止最困难的事情已经过去了，后面都是一键式的了，喝杯茶🍵再继续。

## 在服务器安装ss

因为我是用mac的，考虑到大多数人还是使用windows为主，我就把我的旧电脑给翻出来开机继续做教程。基于windows 7。

- **如果你是用mac，那恭喜你，下面连接的步骤直接打开终端输入**

`ssh root@你的服务器IP地址`

**连接就可以，然后可以跳过安装并运行xshell这个步骤**



- **如果你是用windows10,可以打开系统自带的powershell工具:**





![](./images/powershell_windows_menu.png)

**输入**

`ssh root@你的服务器IP地址`

![](./images/powershell_run.png)

**连接就可以，然后可以跳过安装并运行xshell这个步骤**

### 安装并运行xshell

**提示：如果服务器可以ping通，但xshell无法连接说明服务器被封端口了（很多vultr日本服务器有这种状况），请更换其他地区服务器或者更换服务器商**

windows下ssh连接需要下载Xshell，百度搜一搜就能下载了，当然你也可以用其他的，这里以Xshell为例，安装好Xshell后点击文件-新建

![](./images/w-1.png)

接下来配置连接,名称随便起，主机填写你的服务器IP地址，下面都默认就好

![](./images/w-2.png)

接下来在弹出的窗口填root（默认服务器用户名）

![](./images/w-3.png)

这里就要填入你在上篇复制的服务器密码了

![](./images/w-4.png)

### 安装ss

上面登录成功后如图所示

![](./images/w-5.png)

下面就是精髓的部分了，感谢[@teddysun](https://github.com/teddysun)大佬制作的一键安装脚本，具体更多细节可查看博客：https://teddysun.com/486.html  (由于大佬的[退出](https://teddysun.com/548.html),所以下面的命令目前还可以使用，但是版本已经不再更新)

```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

复制粘贴上面代码到xshell，在xshell要右键粘贴，然后就会有一大串不知名代码蹦出，停在这里了

![](./images/w-6.png)

这时候按一下**Enter回车键**它又可以继续了～

接着又蹦出些东西，是让我们选择ss的服务器端，这里我选择libev版本的，输入**4**按回车

![](./images/libev/1.png)

如同往常，接下来是要填入ss客户端登录的密码，这里我随意填：**abc123456**

![](./images/libev/2.png)

接下来是输入端口号（1-65535任意数字），这里我随便填个**12853**的

![](./images/libev/3.png)

接下来是选择加密方式，最好选择xchacha20-ietf-poly1305，输入**13**

![](./images/libev/4.png)

接着是否需要开启simply-obfs插件，默认就行，**回车**

![](./images/libev/5.png)

接着又是反手一个**回车**就好

![](./images/libev/6.png)

这里可能需要等待一会，看到下图就是大功告成了。干杯🍻！

这个最好截图一下，以防忘记了。

我就当大家英文水平还好吧，下面说的就是你的服务器IP，服务器端端口，密码，加密方式。

![](./images/libev/7.png)

最后需要关闭下系统的防火墙，粘贴下面的命令然后回车就行

```bash
systemctl stop firewalld
systemctl disable firewalld
```

## 下载客户端

如果你跟着我到了这一步就代表安装好了服务器端，但是我们的电脑手机作为客户端也是需要安装客户端软件的。下面是各个终端的下载地址（我用过Windows,MAC,Android,IOS操作起来都是差不多的。）：

Windows：https://github.com/shadowsocks/shadowsocks-windows/releases

Android:https://github.com/shadowsocks/shadowsocks-android/releases

MAC:https://github.com/shadowsocks/ShadowsocksX-NG/releases

Linux:https://github.com/shadowsocks/shadowsocks-qt5/wiki/Installation



IOS: 

由于国区APP下架VPN类APP，包括支持ss类的APP，所以需要切换账号

建议注册一个国外账号，不要国内账号换区，这样既可以需要下载国内APP时切换国区账号，需要下载国外APP时切换外区账号。

**建议不想折腾的可以淘宝买一个国外App Store账号，便宜方便快捷**

步骤：

1.获取一个国区以外的账号

注册国外appid教程：https://www.zhihu.com/question/26458172

（相关注册外区账号教程很多，可以自行搜索）

**由于苹果的新政策，注册apple id在付款方式选择的时候非当地ip无法选择none选项，例如我注册英国区账号，需要ip为英国才可以。即在注册时要搭梯子，对应ip注册。**

2.在APPStore中切换为国区以外账号

3.在AppStore搜索**Potatso Lite**安装

注：或者其他支持shadowsocks的APP也可以，这里比较推荐Potatso Lite
- [Potatso Lite](https://itunes.apple.com/us/app/potatso-lite/id1239860606?mt=8)

  

**下面以windows为例演示：**

打开上方网址下载客户端：

![](./images/libev/9.png)

接着解压后打开Shadowsocks.exe，右击右下角小飞机，点击服务器-编辑服务器：

![](./images/edit_server.png)

还记得上面建议保存的图片吗？这里就用到了，服务器IP，端口，密码，加密方式，然后点击确定

![](./images/libev/8.png)



最后确保打开了PAC模式：

![](./images/super_easy_shadowsocks_tutorial/21.png)



- 这里简要说一下PAC模式和全局模式问题：

PAC模式就是访问国内网站会走国内IP，访问被封的网站走服务器IP

全局就是全部走服务器IP

这里建议选择PAC模式，PAC的地址都是保存在[gfwlist](https://github.com/gfwlist/gfwlist)

希望大家遇到PAC无法访问的网站多上去提issues。

### 神圣时刻

接着最神圣的时刻来了，在浏览器输入google.com，回车，蹦，谷歌回来了

![](./images/w-16.png)

# 补充
## 设置多端口

[如何启用 Shadowsocks 的多端口](https://stanleyzhao.xyz/2019/06/01/%E5%A6%82%E4%BD%95%E5%90%AF%E7%94%A8Shadowsocks%E7%9A%84%E5%A4%9A%E7%AB%AF%E5%8F%A3/)

## 常用命令
start 启动
stop 停止
restart 重启
status 状态

### Shadowsocks-libev 版：
``` bash
/etc/init.d/shadowsocks-libev start
/etc/init.d/shadowsocks-libev stop
/etc/init.d/shadowsocks-libev restart
/etc/init.d/shadowsocks-libev status
```

### Shadowsocks-Python 版：
``` bash
/etc/init.d/shadowsocks-python start
/etc/init.d/shadowsocks-python stop
/etc/init.d/shadowsocks-python restart
/etc/init.d/shadowsocks-python status
```

### ShadowsocksR 版：
``` bash
/etc/init.d/shadowsocks-r start
/etc/init.d/shadowsocks-r stop
/etc/init.d/shadowsocks-r restart
/etc/init.d/shadowsocks-r status
```

### Shadowsocks-Go 版：
``` bash
/etc/init.d/shadowsocks-go start
/etc/init.d/shadowsocks-go stop
/etc/init.d/shadowsocks-go restart
/etc/init.d/shadowsocks-go status
```

## 如何卸载
运行如下命令，根据提示，选择对应版本卸载
``` bash
./shadowsocks-all.sh uninstall
```
# 最后

最后，这里我们的任务完成了，但是如果你想优化一下服务器连接，可以安装BBR加速。具体可以看这篇文章：[文章](https://teddysun.com/489.html)

> 使用root用户登录，运行以下命令：
>
> ```bash
> wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
> ```
>
> 安装完成后，脚本会提示需要重启 VPS，输入 y 并回车后重启。

# Q&A

汇总一些邮件反馈的问题

**[已解决]1.问题：-bash: wget: command not found
环境：服务器：linode，新加坡服务器，cent os7**  

可以参照这篇文章安装wget后尝试：https://www.wn789.com/5624.html

**[已解决]2.问题:当访问Google学术时，会提示 : 
"We are sorry, but your computer or network may be sending automated queries. To protect our users, we can't process your request right now "**  

由于谷歌有自己的一套反代理爬虫的机制，所以很多 VPS 的 ip 会被谷歌检测到是代理，遇到这种情况可以用更换服务器即可，如果没有其他服务器可用，可以用**谷歌学术镜像**。

**[已解决]3.[Error] Failed to install python**  

由于以前文章的centos7不可选导致选择centos8导致的无法安装问题可以尝试使用**Debian10x64或者CentOS7**系统安装[#27](https://github.com/zhaoweih/Shadowsocks-Tutorial/issues/27)

**[已解决]4.可以ping通但是连不上**

这个情况有可能是防火墙没关闭，因为现在vultr默认会打开防火墙，所以需要关闭防火墙端口才能通

```bash
systemctl stop firewalld
systemctl disable firewalld
```

执行后可以用`systemctl status firewalld`查看是否已经关闭，显示inactive就是关闭了防火墙

![](./images/firewall_inactive.png)

[在线检查端口是否开放](https://tool.chinaz.com/port)

# 讨论
## Discord
**我创建了一个Discord讨论组，遇到问题的小伙伴可以一起讨论**

[![Discord](./images/discord.svg)](https://discord.gg/wHFxCVk)

# 更多
**如果想详细了解有关shadowsocks翻墙知识的小伙伴可以查看下面文章**

- [浅谈vpn、vps、Proxy以及shadowsocks之间的联系和区别](https://medium.com/@thomas_summon/%E6%B5%85%E8%B0%88vpn-vps-proxy%E4%BB%A5%E5%8F%8Ashadowsocks%E4%B9%8B%E9%97%B4%E7%9A%84%E8%81%94%E7%B3%BB%E5%92%8C%E5%8C%BA%E5%88%AB-b0198f92db1b)

# 建议

如果大家对这篇文章有任何疑问都可以提[issues](https://github.com/zhaoweih/Shadowsocks-Tutorial/issues)，如果你有其他更简单或者其他方法翻墙也可以pull requests。

# 关于

我是一名初入社会的社畜，一个追求自由的少年，如果想要找我，可以给我发邮件📧

📮我的邮箱：zhaoweihao.dev@gmail.com

# 赞赏

我目前生活还是蛮自如的，有吃的有喝的，就不用赞赏了。喜欢就给我个star或者fork一下吧❤️，谢谢！
