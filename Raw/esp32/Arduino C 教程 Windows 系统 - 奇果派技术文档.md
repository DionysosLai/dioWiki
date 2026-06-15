---
title: "Arduino C 教程 Windows 系统 - 奇果派技术文档"
source: "https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/5.Arduino%20C%20%E6%95%99%E7%A8%8B%20Windows%20%E7%B3%BB%E7%BB%9F/#01-hello-world"
author:
published:
created: 2026-05-05
description:
tags:
  - "clippings"
---
## 5\. Arduino C 教程 Windows 系统

## 开发环境设置

**1.Windows系统：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/eef95a4f71c9316e41a35813556e51da.png)

**1.1. 下载安装Arduino软件：**

(1)首先，进入Arduino官方网站： [https://www.arduino.cc/](https://www.arduino.cc/) ，点击“ **SOFTWARE** ”进入下载页面，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/09ba2def88a3782a4ea7fde0a3fa94b2.png)

(2)然后，根据你的操作系统选择并下载相应的安装程序。如果你是Windows用户，请选择安装2.0.3版本Arduino IDE，当点击“ **Windows** Win10 and newer,64bits”或“ **Windows** MSI installer”，代表下载安装文件(.exe) ，需要手动安装; 当点击“ **Windows** ZIP file”，代表直接下载2.0.3版本Arduino IDE，是一个压缩文件，解压就可以直接使用，无需安装。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1f975a2153559093d34b23afd73d3e7e.png)

点击DOWNLOAD图标，就可直接安装Arduino IDE了。 请注意：我们撰写文档的时候版本是2.x版本，如果您下载的时候有ArduinoIDE更新了，请下载最新版本。

(3)Arduino软件下载完成后,如果你是点击“ **Windows** Win10 and newer,64bits”或“ **Windows** MSI installer”下载的(.exe)文件“ **arduino-ide\_x.x.x\_Windows\_64bit.exe** ” ，需要双击(.exe)文件继续安装，当你收到操作系统的警告时，请点击“ **Allow access** ”允许驱动程序安装。首先点击“ **I Agree** ”, 然后选择“Anyone who uses this computer(all users)”后再点击Next。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/15bcc3e3f13e89a7a3eaf2d6e9ca5b2c.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/72f9cfe86a9e89aeb3431ed66d9df5bf.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/74bde0fbec2a9a15c14435633eb7b864.png)

(4)点击“ **Next** ”后，如果又出现下面页面，点击“ **I Agree** ”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/72f9cfe86a9e89aeb3431ed66d9df5bf.png)

(5)选择安装目录(我们建议保持默认目录)，然后点击“ **Install** ”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6c66ee9c7b948a27341de4f5d5e59ab1.png)

(6)如果出现以下界面，则应选择“ **Install** ”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/68ddbb0c391855fd7213d5d9269eebb8.png)

该过程将提取并安装所有必需的文件，以正确执行Arduino软件(IDE)。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/235841a13809e8c131e0471e1c90f54a.png)

(7)安装完成后，会在桌面上生成一个Arduino IDE软件快捷方式。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9152131aa663c6c5723a96c9e6f7a9a1.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/edbce928e27d6f6d26e0a6df960421da.png)

**1.2. 在Windows系统上安装驱动：**

（注意：如果电脑已经安装了驱动程序，则不需要再安装驱动；如果没有，则需要进行以下操作） 在使用ESP32主板之前，必须安装驱动程序，否则ESP32主板将无法与计算机通信。将主控板用USB线连接在电脑上，一般MacOS和Windows10系统的电脑会自动安装驱动。如果没能自动安装，则需要手动安装驱动。

（1）查看电脑是否自动安装好驱动：

点击“ **计算机** ”--“ **属性** ”--“ **设备管理器** ”，显示如下图是安装成功的了：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/65fcafd5ca192f1d566779a819e04d00.png)

（2）如果没能自动安装，那就手动安装，步骤如下：

我们在下载资料里提供了串口驱动

但如果您想下载最新的驱动，可以点击链接： [https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads)

右键点击“ **USB Serial** ”，选择“ **更新驱动程序(P)** ”并点击。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/594f6af70f127b4d54239b5af7f11035.png)

跳转到以下页面，选择“ **浏览我的电脑以查找驱动程序(R)** ”并点击。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e71e6ab23f4ecbd2c0eefb662aa50704.png)

我们在对应的文件夹中也提供有CP210X系列芯片的驱动文件，位置如下：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/93db967482f9b80ea98c483c8f56e498.png)

你可以先将驱动文件复制到电脑桌面上，然后点击“ **浏览(R)...**”,选中CP210X系列芯片的驱动，最后点击“ **下一页** ”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c6fd62ecfbcc250b725abdc72f075f0c.png)

过一会儿，驱动安装成功。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/f7439196fa8567c300f0f3e35e399d42.png)

这个时候再打开计算机设备管理器，就可以看到CP2102驱动程序已经安装成功了，刚刚那个黄色的感叹号不见了。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/65fcafd5ca192f1d566779a819e04d00.png)

**1.3. Arduino IDE工具栏介绍：**

点击电脑桌面上的图标 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2aa3f7ff09ff5841f1864c521af61cbd.png) ，打开Arduino IDE。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/29c35817885841cbbc9385b4c8544290.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/752c9d4cfd38937d8b7ac496bf66d96d.png) -- 用于检查是否存在任何编译错误。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/84910ad669ba1c2669ec402831fbf46d.png) -- 用于将程序上传到Arduino板。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d72475c82afbebb1edf0f1fc12da524.png) -- 用于编写程序时的单步调试。

\-- 用于从板接收串行数据并将串行数据发送到板的串行监视器。

\-- 用于串口接收的数据转换成动态曲线图。

\-- 用于打开最近保存的示例草图。

\-- 用手动安装开发板。

**语言切换功能：**

（1）单击“File”→“Preferences”，在 Preferences 页面中将语言“English”切换成“简体中文”，点击“OK”就可以了。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d44b87180f422631e697fea296cd5582.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d77dd3ef192740d9e3d321ea94eaa105.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9f65a26855c17f2e34252ffab28c51d7.png)

**1.4. 在Arduino IDE上安装ESP32：**

上面已经学习了怎么下载ArduinoIDE和怎么安装驱动，那下面就要在Arduino IDE上安装ESP32，请执行以下步骤：

特别注意：你需要Arduino IDE 1.8.5或更高版本才能在其上安装ESP32。

(1)点击电脑桌面上的图标 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2aa3f7ff09ff5841f1864c521af61cbd.png) ，打开Arduino IDE。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e50b14820c3bb291108dc8b9c0c12843.png)

(2)点击“文件” →“首选项”，如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/339b8fc0220890b15cac94e7fefd8e7f.png)

(3)打开下图标出的按钮。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/dd6d3ea8b8ab77977ff2b2af74dbcdab.png)

(4)将这个地址：https://espressif.github.io/arduino-esp32/package\_esp32\_index.json ，复制粘贴到里面去再点击“ **确定** ”保存这个地址，如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0f4697f40d0e4c73cdaa36a15034e63b.png)

(5)再点击“ **确定** ”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/11dd827f1f17588419bf7be03a47211d.png)

（6）先点击“ **工具** ”→“ **开发版:**”，，再点击“ **开发版管理器...**”进入“ **开发版管理器** ”页面，在文本框中输入“ **esp32** ”，选择 1.06 版本进行安装，安装包不大，点击“ **安装** ”开始安装相关安装包。如下图： **（特别提醒：选择更高版本或最新版本，可能会出现安装失败。）**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/486d22dca3d5007c0866fe0dd4b3075e.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/fe7700b0fc213248a7b2d09cd6fc20ae.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/26766efa26cbdaa8d507e52e40317158.png)

（7）点击“ **工具** ”→“ **开发版:**”，就可以看到安装好的ESP32 Arduino，你可以在里面查看到各种不同型号ESP32开发板，选择对应的ESP32开发板型号。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7d924f3a05df1addfd61d26a4b1f9eff.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/07bbc456b0cfb6f5393701025233b38b.png)

（8）设置好板型后，再选择正确的COM口（安装驱动成功后可看到对应COM口），设置如下图。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/65fcafd5ca192f1d566779a819e04d00.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2cdb1757b32b480e3dac16aa8385e13c.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3270b2614818b728fb3fdd4a45d239f9.png)

**2.Mac系统:**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a94b5505840316715e4bc9badd0421ba.png)

**2.1.下载安装Arduino IDE:**

进入Arduino官方网站： [https://www.arduino.cc/](https://www.arduino.cc/) ，点击“ **SOFTWARE** ”进入下载页面，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2f1dce89630be8f6fdd23f38ab7097ec.png)

2.2.如何安装CP2102驱动程序：

（注意：如果已经安装了驱动程序，则不需要再安装驱动；如果没有，则需要进行以下操作）

（1）用USB线将ESP32主板连接到你的MacOS系统电脑上，并打开Arduino IDE。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a694cc7590112b9becc48e0ad33588ea.png)

（2）CP2102驱动下载链接： [https://cn.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads](https://cn.silabs.com/developers/usb-to-uart-bridge-vcp-drivers?tab=downloads)

（3）点击下载MacOS 版本。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a369c4f935f32560b84a9b3b756c7dc5.png)

（4）解压下载好的压缩包。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/43f7535e76478fc4bd3b4f7eb68ee068.png)

（5）打开文件夹，双击“SiLabsUSBDriverDisk.dmg”文件。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e339ed4aa5a59a7ffbd426da57cc5522.png)

可以看到以下文件。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/ba80c239c55f073ad212e110898c9be5.png)

（6）双击“Install CP210x VCP Driver”，勾选“Don’t warn me when opening application on this disk image”并单击“Open”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b90f9fec981a67e60d6d973049c8480a.png)

（7）单击“Continue”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e3d154346714057c76f44a429d30bc75.png)

（8）先点击“Agree”，然后点击“Continue”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/df1b62568fc2737d10bebe86364d6240.png)

（9）继续点击“Continue”，然后输入你的用户密码

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d5943e9f17648b7b345f2cb921835e8.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d5caae5575fc94e8837daad3675dd164.png)

（10）选择“Open Security Preferences”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/36fe00477b7e98da6cd3f7d4e074c489.png)

（11）点击安全锁，输入你的用户密码来授权。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4889cc38b37c1df851c7772aa1f74fe4.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/85556fc015cdb38f51375ace1c498585.png)

（12）看到锁被打开了，点击“Allow”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/bb9213f09221a88dee06bd4ff792cd4c.png)

（13）回到安装界面，根据提示等待安装.

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d49cc02c9a101542bb4a8572da600dfe.png)

（14）安装成功

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/dead537234a8ea1a9feab59bc451eebb.png)

**2.3. 在Arduino IDE上安装ESP32：**

上面已经学习了怎么下载ArduinoIDE和怎么安装驱动，那下面就要在Arduino IDE上安装ESP32，请执行以下步骤：

特别注意：你需要Arduino IDE 1.8.5或更高版本才能在其上安装ESP32。

(1)点击电脑桌面上的图标 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2aa3f7ff09ff5841f1864c521af61cbd.png) ，打开Arduino IDE。点击“Arduino IDE” →“首选项”，如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9a1550c0afb15d603b35c14fc7eb9419.png)

(2)打开下图标出的按钮

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2021aa4872f771da0789e9d4d35c778e.png)

(3)将这个地址：https://espressif.github.io/arduino-esp32/package\_esp32\_index.json ，复制粘贴到里面去再点击“ **确定** ”保存这个地址，如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/01edadc223784029595d586e60336c53.png)

(4)再点击“ **确定** ”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/f88fa3fc64d0e88254ed1f1960ff20cf.png)

（5）先点击“ **工具** ”→“ **开发版:**”，再点击“ **开发版管理器...**”进入“ **开发版管理器** ”页面，在文本框中输入“ **esp32** ”，选择 1.06 版本进行安装，安装包不大，点击“ **安装** ”开始安装相关安装包。如下图： **（特别提醒：选择更高版本或最新版本，可能会出现安装失败。）**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b190d9a92887ff3f38fd00f9ef6a7bc6.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1bd7dab9f8e78713466d019451110080.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/032996f813d7ccb74e4c6c8a6a13486f.png)

（6）点击“ **工具** ”→“ **开发版:**”，就可以看到安装好的ESP32 Arduino，你可以在里面查看到各种不同型号ESP32开发板，选择对应的ESP32开发板型号。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/40fb53f0ba1f781650ac404f8a25a3b6.png)

（7）设置好板型后，再选择正确的COM口（安装驱动成功后可看到对应COM口），设置如下图。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b240beb6e80e99e17c399d6d65f03fb4.png)

**3.添加arduino库文件**

特别提醒：库文件在上面 **资料下载** 处提供有，请下载并且安装好库文件。

我们提供的Arduino库文件的路径：**..\\库文件\\Arduino\_C\_Windows系统** 。如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/77709bd58e52d152eb32bf471cd69797.png)

**3.1. Windows系统添加库文件的方法：**

（1）打开Arduino IDE ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0c8b09d3670cabd5f21d0528dd77796b.png) ，在Arduino IDE界面点击“ **项目** ”→“ **包含库** ”→“ **添加.ZIP库...**”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/f82d63c799a10f799b43831596ea78cc.png)

（2）找到库文件存放的位置，选中对应的库文件，点击“ **打开** ”添加即可。库文件只能一个一个的添加。（注意：库文件需要压缩为 **.ZIP格式** ；这里以“ **LCD\_128X32.ZIP** ”为演示，其他库文件的添加方法是一样的。） ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4f8e2aa97ec10a02199a4073da86b6c9.png)

**3.2. MacOS系统的库文件添加方法:**

（1）arduino IDE界面选择“ **项目** ” > “ **包含库** ” > “ **添加.ZIP库...**”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/bc7d51aaa2a4b4f2c3bacfc3f46ebd7f.png)

（2）找到库文件存放的位置，选中对应的库文件，点击“ **打开** ”添加即可。库文件只能一个一个的添加。（这里以添加“LCD\_128X32.ZIP”库文件为例，其他库文件的添加方法一样），如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/664873470abd9adca53197510237a958.png)

到这，正常是安装成功的了。

## 项目01 Hello World

**1.项目介绍：**

对于ESP32的初学者，我们将从一些简单的东西开始。在这个项目中，你只需要一个ESP32主板，USB线和电脑就可以完成“Hello World!”项目。它不仅是ESP32主板和电脑的通信测试，也是ESP32的初级项目。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| --- | --- |
| ESP32\*1 | USB 线\*1 |

**3.项目接线：**

在本项目中，我们通过USB线将ESP32和电脑连接起来。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/46cf3a8a1c79e456ac0f02da5ef38aec.png)

**4.项目代码：**

你可以打开我们提供的代码，本项目中使用的代码保存在（即路径)：**..\\程序代码\\Arduino\_C\_Windows系统\_代码** 。（ **后面的项目代码保存的路径也一样，即：同下。** ）

```js
//*************************************************************************************
/*
 * 文件名 : Hello World
 * 描述 : 输入字母R，串口显示“Hello World”.
*/
char val;// 定义变量val
void setup()
{
Serial.begin(115200);// 设置波特率为115200
}
void loop()
{
  if (Serial.available() > 0) {
    val=Serial.read();// 读取并赋值给val
    if(val=='R')// 检查输入的字母“R”
    {  // 如果是这样的话,    
     Serial.println("Hello World!");// 显示“Hello World !”.
    }
  }
}
//*************************************************************************************
```
在上传项目代码到ESP32之前，点击“ **工具** ”→“ **开发板** ”，选择“ **ESP32 Wrover Module** ”。（ **后面上传项目代码的步骤也一样，即：同下。** ）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e4e1d7c497e408cf00cebfa63b6ef7eb.png)

选择正确的端口（COM）。(注意：将ESP32主板通过USB线连接到计算机后才能看到对应的端口。)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/f4a42b32cce8472c2033bab12ad2c82a.png)

注意：对于macOS用户，如果上传失败，在单击之前，请设置波特率为 **115200** 。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8c7346fc57c7d09ec75b5fb60d83ef67.png)

单击将项目代码上传到ESP32主板上。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/ce1eafb4ca085a0e732750e1dfec0fd9.png)

注意： 如果上传代码不成功，可以再次点击后用手按住ESP32主板上的Boot键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a3ce49fbd6f40f09869aa7e1d9f902f8.png) ，出现上传进度百分比数后再松开Boot键，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0fdadbed575d5f54ae1ca9405c3647cb.png)

项目代码上传成功！

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/429b498bda8415f70866f28e6b9569ed.png)

**5.项目结果：**

项目代码上传成功后，单击图标进入串行监视器，设置波特率为115200，在文本框输入字母“R”，按下回车键(Enter 键)，这样串口监视器打印“Hello World!”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/131f86617fbf04fbccc219bdd8a83111.png)

## 项目02 点亮LED

**1.项目介绍：**

在这个项目中，我们将向你展示点亮LED。我们使用ESP32的数字引脚打开LED，使LED被点亮。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 红色LED\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*1 | 跳线\*2 | USB 线\*1 |

**3.元件知识：**

**（1）LED**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/32369e035f09fa12898e90cd3f916cbe.png)

LED是一种被称为“发光二极管”的半导体，是一种由半导体材料(硅、硒、锗等)制成的电子器件。它有正极和负极。短腿为负极，接GND，长腿为正极，接3.3V或5V。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cbb16ef4d8cb62a4001d1a05ae3ac615.png)

**（2）五色环电阻**

电阻是电路中限制或调节电流流动的电子元件。左边是电阻器的外观，右边是电阻在电路中表示的符号。电阻(R)的单位为欧姆(Ω)，1 mΩ= 1000 kΩ，1kΩ= 1000Ω。

我们可以使用电阻来保护敏感组件，如LED。电阻的强度（以Ω为单位）用小色环标记在电阻器的主体上。每种颜色代表一个数字，你可以用电阻对照卡查找。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e60472f717ced1cc0bd94f4972ef0cd0.png)

在这个套件中，我们提供了3个具有不同电阻值的五色环电阻。这里以3个五色环电阻为例： 220Ω电阻×10

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/951ce7d7778b34bf8fbdb3de1b8c3116.png)

10KΩ电阻×10

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2d3f0996515352c92c07d17e074e7308.png)

1KΩ电阻×10

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/931d1535563f6d817300f97c0946a01c.png)

在相同的电压下，会有更小的电流和更大的电阻。电流、电压、电阻之间的联系可以用公式表示：I=U/R。在下图中，目前通过R1的电流: I = U / R = 3 V / 10 KΩ= 0.0003A= 0.3mA。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/997accddd11490d13b769b9f3477daa7.png)

不要把电阻值很低的电阻直接连接在电源两极，这样会使电流过高而损坏电子元件。电阻是没有正负极之分。

**（3）面包板**

面包板是实验室中用于搭接电路的重要工具。面包板上有许多孔，可以插入集成电路和电阻等电路元件。熟练掌握面包板的使用方法是提高实验效率，减少实验故障出现几率的重要基础之一。下面就面包板的结构和使用方法做简单介绍。一个典型的面包板如下所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d4cb46e6b01d820bb150f0e8a122417e.png)

面包板的外观和内部结构如上图所示，常见的最小单元面包板分上、中、下三部分，上面和下面部分一般是由一行或两行的插孔构成的窄条，中间部分是由中间一条隔离凹槽和上下各5 行的插孔构成的条。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2e9ef45f88652609af40b031ed0f0084.png)

在面包板的两个窄条分别有两行插孔，两行之间是不连通的，一般是作为电源引入的通路。上方第一行标有“+”的一行有10组插孔（内部都是连通），均为正极；上方第二行标有“-”的一行有10组插孔，（内部都是连通），均为接地。面包板下方的第一行与第二行结构同上。如需用到整个面包板，通常将“+”与“+”用导线连接起来，“-”与“-”用导线连接起来。

中间部分宽条是由中间一条隔离凹槽和上下各5 行的插孔构成。在同一列中的5 个插孔是互相连通的，列和列之间以及凹槽上下部分则是不连通的。外观及结构如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8690e125a8f918f5e2b7950526e98ebb.png)

中间部分宽条的连接孔分为上下两部分，是面包板的主工作区，用来插接原件和跳线。在同一列中的5个插孔（即a-b-c-d-e，f-g-h-i-j）是互相连通的；列和列之间以及凹槽上下部分是不连通的。在做实验的时候，通常是使用两窄一宽组成的小单元，在宽条部分搭接电路的主体部分，上面的窄条取一行做电源，下面的窄条取一行做接地。中间宽条用于连接电路，由于凹槽上下是不连通的，所以集成块一般跨插在凹槽上。

**(4)电源**

ESP32需要3.3V-5V电源，在本项目中，我们通过用USB线将ESP32和电脑连起来。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/46cf3a8a1c79e456ac0f02da5ef38aec.png)

**4.项目接线图：**

首先，切断ESP32的所有电源。然后根据接线图搭建电路。电路搭建好并验证无误后，用USB线将ESP32连接到电脑上。

注意：避免任何可能的短路(特别是连接3.3V和GND)!

警告：短路可能导致电路中产生大电流，造成元件过热，并对硬件造成永久性损坏。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6d79aa47daab6bc5dc46b1e62215d9c8.png)

注意:

怎样连接LED

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cbb16ef4d8cb62a4001d1a05ae3ac615.png)

怎样识别五色环220Ω电阻

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/951ce7d7778b34bf8fbdb3de1b8c3116.png)

**5.项目代码：**

你可以打开我们提供的代码，本项目中使用的代码保存在（即路径)：**..\\程序代码\\Arduino\_C\_Windows系统\_代码** 。（ **后面的项目代码保存的路径也一样，即：同下。** ）

```js
//**********************************************************************
/*
 * 文件名 : 点亮LED
 * 描述 : 点亮一盏灯.
*/
#define LED_BUILTIN 15

// 复位或单板上电时，setup功能只运行一次
void setup() {
  // 初始化数字引脚LED_BUILTIN作为输出.
  pinMode(LED_BUILTIN, OUTPUT);
}
void loop() {
  digitalWrite(LED_BUILTIN, HIGH);   // 打开LED (HIGH为高电平)
}
//*************************************************************************************
```
在上传项目代码到ESP32之前，点击“ **工具** ”→“ **开发板** ”，选择“ **ESP32 Wrover Module** ”。（ **后面上传项目代码的步骤也一样，即：同下。** ）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9b0703c5d3b7f1bdcd8de3bfe70eff7c.png)

选择正确的端口（COM）。(注意：将ESP32主板通过USB线连接到计算机后才能看到对应的端口。)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/28bd4716860a4962f12d7ffe42b989a8.png)

注意：对于macOS用户，如果上传失败，在单击之前，请设置波特率为 **115200** 。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b737d2c5724e49b69ab472e2b369e543.png)

单击将项目代码上传到ESP32主板上。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e32cec541e8479b04aa7eddfa26045e2.png)

注意： 如果上传代码不成功，可以再次点击后用手按住ESP32主板上的Boot键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a3ce49fbd6f40f09869aa7e1d9f902f8.png) ，出现上传进度百分比数后再松开Boot键，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0fdadbed575d5f54ae1ca9405c3647cb.png)

项目代码上传成功！

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/413f0f5221cedb45f82c108f6b5e1ab2.png)

**6.项目现象：**

项目代码上传成功后，利用USB线供电，LED被点亮。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/96672306fbbac2d8b33a55315d0d8fc5.png)

## 项目03 LED闪烁

**1.项目介绍：**

在这个项目中，我们将向你展示LED闪烁效果。我们使用ESP32的数字引脚打开LED，让它闪烁。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 红色LED\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*1 | 跳线\*2 | USB 线\*1 |

**3.项目接线图：**

首先，切断ESP32的所有电源。然后根据电路图和接线图搭建电路。电路搭建好并验证无误后，用USB线将ESP32连接到电脑上。

注意：避免任何可能的短路(特别是连接3.3V和GND)!

警告：短路可能导致电路中产生大电流，造成元件过热，并对硬件造成永久性损坏。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6d79aa47daab6bc5dc46b1e62215d9c8.png)

注意:

怎样连接LED

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cbb16ef4d8cb62a4001d1a05ae3ac615.png)

怎样识别五色环220Ω电阻

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/951ce7d7778b34bf8fbdb3de1b8c3116.png)

**4.项目代码：**

你可以打开我们提供的代码，本项目中使用的代码保存在（即路径)：**..\\程序代码\\Arduino\_C\_Windows系统\_代码** 。（ **后面的项目代码保存的路径也一样，即：同下。** ）

```js
//**********************************************************************
/*
 * 文件名 : LED 闪烁
 * 描述 : 让led闪烁.
*/
#define PIN_LED   15   //定义led引脚

// 复位或单板上电时，setup功能只运行一次
void setup() {
  // 初始化数字引脚LED作为输出.
  pinMode(PIN_LED, OUTPUT);
}

// 循环函数永远重复运行
void loop() {
  digitalWrite(PIN_LED, HIGH);   // 打开LED (HIGH为高电平)
  delay(500);                       // 延时 0.5s
  digitalWrite(PIN_LED, LOW);    // 关闭LED，使电压低
  delay(500);                       // 延时 0.5s
}
//*************************************************************************************
```
在上传项目代码到ESP32之前，请检查Arduino IDE的配置。（ **后面上传项目代码的步骤也一样，即：同下。** ）

单击“ **工具** ”，确认“ **开发板** ”板型和“ **端口(COM)** ”，如下所示：(注意：将ESP32主板通过USB线连接到计算机后才能看到对应的端口。)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/dd983623a3bd853f4d8d567d8c0d0e63.png)

单击将项目代码上传到ESP32主板上。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7293f3ff10f053da942b3d72c31a6517.png)

注意： 如果上传代码不成功，可以再次点击后用手按住ESP32主板上的Boot键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a3ce49fbd6f40f09869aa7e1d9f902f8.png) ，出现上传进度百分比数后再松开Boot键，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0fdadbed575d5f54ae1ca9405c3647cb.png)

项目代码上传成功！

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4c4de10253147bcb786652053647f5f3.png)

**5.项目现象：**

项目代码上传成功后，利用USB线上电，可以看到的现象是：可以看到电路中的LED会反复闪烁。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/bbd161f32b47a175d78773391bd7b0c1.png)

## 项目04 呼吸灯

**1.项目介绍：**

在之前的研究中，我们知道LED有亮/灭状态，那么如何进入中间状态呢?如何输出一个中间状态让LED“半亮”?这就是我们将要学习的。呼吸灯，即LED由灭到亮，再由亮到灭，就像“呼吸”一样。那么，如何控制LED的亮度呢?我们将使用ESP32的PWM来实现这个目标。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 红色LED\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*1 | 跳线\*2 | USB 线\*1 |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e739a6e4a95fa8bbbefb26ef955dc465.png)

**模拟信号 & 数字信号**

模拟信号在时间和数值上都是连续的信号。相反，数字信号或离散时间信号是由一系列数字组成的时间序列。生活中的大多数信号都是模拟信号，一个熟悉的模拟信号的例子是：全天的温度是连续不断变化的，而不是突然从0到10的瞬间变化。然而，数字信号的值可以瞬间改变。这个变化用数字表示为1和0(二进制代码的基础)。如下图所示，我们可以更容易地看出它们的差异。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/550c1d587189ce5ac3678f44b08ac888.png)

在实际应用中，我们经常使用二进制作为数字信号，即一系列的0和1。由于二进制信号只有两个值(0或1)，因此具有很大的稳定性和可靠性。最后，可以将模拟信号和数字信号相互转换。

**PWM：**

脉宽调制(PWM)是一种利用数字信号控制模拟电路的有效方法。普通处理器不能直接输出模拟信号。PWM技术使这种转换(将数字信号转换为模拟信号)非常方便。PWM技术利用数字引脚发送一定频率的方波，即高电平和低电平的输出，交替持续一段时间。每一组高电平和低电平的总时间一般是固定的，称为周期(注:周期的倒数是频率)。高电平输出的时间通常称为脉宽，占空比是脉宽(PW)与波形总周期(T)之比的百分比。高电平输出持续时间越长，占空比越长，模拟信号中相应的电压也就越高。下图显示了对应于脉冲宽度0%-100%的模拟信号电压在0V-3.3V(高电平为3.3V)之间的变化情况.

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0c29da4ca7a2fee2f5a0078eacc9e88a.png)

PWM占空比越长，输出功率越高。既然我们了解了这种关系，我们就可以用PWM来控制LED的亮度或直流电机的速度等等。从上面可以看出，PWM并不是真实的模拟信号，电压的有效值等于相应的模拟信号。因此，我们可以控制LED和其他输出模块的输出功率，以达到不同的效果。

**ESP32 与 PWM**

在ESP32上，LEDC(PWM)控制器有16个独立通道，每个通道可以独立控制频率，占空比，甚至精度。与传统的PWM引脚不同，ESP32的PWM输出引脚是可配置的，每个通道有一个或多个PWM输出引脚。最大频率与比特精度的关系如下公式所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c6afada712f786a0827ced2172fed124.png)

其中比特的最大值为31。例如: 生成PWM的8位精度(2ˆ8 = 256。取值范围为0 ~ 255)，最大频率为80,000,000/255 = 312,500Hz。)

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6d79aa47daab6bc5dc46b1e62215d9c8.png)

**5.项目代码：**

本项目设计使GPIO15 输出PWM，脉宽由0%逐渐增加到100%，再由100%逐渐减小到0%。

你可以打开我们提供的代码，本项目中使用的代码保存在（即路径)：**..\\程序代码\\Arduino\_C\_Windows系统\_代码** 。（ **后面的项目代码保存的路径也一样，即：同下。** ）

```js
//**********************************************************************
/*
 * 文件名 : 呼吸灯
 * 描述 : 让led灯像呼吸一样忽亮忽灭.
*/
#define PIN_LED   15   //定义led引脚
#define CHN       0   //定义PWM通道
#define FRQ       1000  //定义PWM频率
#define PWM_BIT   8     //定义PWM精度
void setup() {
  //如果你安装的esp32开发板是2.X，请用这段代码
  ledcSetup(CHN, FRQ, PWM_BIT); //设置PWM通道
  ledcAttachPin(PIN_LED, CHN);  //将led引脚连接到PWM通道

  //如果你安装的esp32开发板是3.X及更新，请用这段代码
  // ledcAttach(CHN, FRQ, PWM_BIT); //设置PWM通道
  // ledcWrite(PIN_LED, CHN);  //将led引脚连接到PWM通道
}

void loop() {
  for (int i = 0; i < 255; i++) { //让LED逐渐变亮
    ledcWrite(CHN, i);
    delay(10);
  }
  for (int i = 255; i > -1; i--) {  //让LED逐渐变暗
    ledcWrite(CHN, i);
    delay(10);
  }
}
//*************************************************************************************
```
在上传项目代码到ESP32之前，请检查Arduino IDE的配置。（ **后面上传项目代码的步骤也一样，即：同下。** ）

单击“ **工具** ”，确认“ **开发板** ”板型和“ **端口(COM)** ”，如下所示：(注意：将ESP32主板通过USB线连接到计算机后才能看到对应的端口。)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a4061989d2cb2a0c1a05207221a4e4fd.png)

单击将项目代码上传到ESP32主板上。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e09f8dc6c566e5779e998429cc3bf36e.png)

注意： 如果上传代码不成功，可以再次点击后用手按住ESP32主板上的Boot键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a3ce49fbd6f40f09869aa7e1d9f902f8.png) ，出现上传进度百分比数后再松开Boot键，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0fdadbed575d5f54ae1ca9405c3647cb.png)

项目代码上传成功！

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3e171796edf4758bb1835fafc8750ea8.png)

**6.项目现象：**

项目代码上传成功后，利用USB线上电，可以看到的现象是：电路中的LED从暗逐渐变亮，再从亮逐渐变暗，就像呼吸一样。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7a0d2717b48056cbef36f880212d8e07.png)

## 项目05 交通灯

**1.项目介绍：**

交通灯在我们的日常生活中很普遍。根据一定的时间规律，交通灯是由红、黄、绿三种颜色组成的。每个人都应该遵守交通规则，这可以避免许多交通事故。在这个项目中，我们将使用ESP32和一些led(红，黄，绿)来模拟交通灯。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 红色LED\*1 | 黄色 LED\*1 |
|  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 绿色LED\*1 | 220Ω电阻\*3 | 跳线若干 | USB 线\*1 |

**3.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6122c8ef01c7a01dc5491df1c73a00b1.png)

**4.项目代码：**

你可以打开我们提供的代码，本项目中使用的代码保存在（即路径)：**..\\程序代码\\Arduino\_C\_Windows系统\_代码** 。（ **后面的项目代码保存的路径也一样，即：同下。** ）

```js
//**********************************************************************
/*
 * 文件名  : 交通灯
 * 描述 : 模拟交通灯.
*/
#define PIN_LED_RED   0   //定义红色引脚
#define PIN_LED_YELLOW   2   //定义黄色引脚
#define PIN_LED_GREEN  15   //定义绿色引脚

void setup() {
  pinMode(PIN_LED_RED, OUTPUT);
  pinMode(PIN_LED_YELLOW, OUTPUT);
  pinMode(PIN_LED_GREEN, OUTPUT);
}

void loop() {
   digitalWrite(PIN_LED_RED, HIGH);// 点亮红色的灯
   delay(5000);// 延时 5 s
   digitalWrite(PIN_LED_RED, LOW); // 熄灭红色的灯
   delay(500);// 延时 0.5 s
   for(int i=0;i<3;i++)// 闪烁 3 次.
  {
   digitalWrite(PIN_LED_YELLOW, HIGH);// 点亮黄色的灯
   delay(500);// 延时 0.5 s
   digitalWrite(PIN_LED_YELLOW, LOW);// 熄灭黄色的灯
   delay(500);// 延时 0.5 s
   } 
   digitalWrite(PIN_LED_GREEN, HIGH);// 点亮绿色的灯
   delay(5000);// 延时 5 s
   digitalWrite(PIN_LED_GREEN, LOW);// 
  delay(500);// 延时 0.5 s
}
//*************************************************************************************
```
在上传项目代码到ESP32之前，请检查Arduino IDE的配置。（ **后面上传项目代码的步骤也一样，即：同下。** ）

单击“ **工具** ”，确认“ **开发板** ”板型和“ **端口(COM)** ”，如下所示：(注意：将ESP32主板通过USB线连接到计算机后才能看到对应的端口。)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c0b41ce0f8f811be5c596c650f9e37a1.png)

单击将项目代码上传到ESP32主板上。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c0aa395baa685d2c783a0da238cd61ac.png)

注意： 如果上传代码不成功，可以再次点击后用手按住ESP32主板上的Boot键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a3ce49fbd6f40f09869aa7e1d9f902f8.png) ，出现上传进度百分比数后再松开Boot键，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0fdadbed575d5f54ae1ca9405c3647cb.png)

项目代码上传成功！

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6540c01185c75398b0822a6dc575f5a4.png)

**5.项目现象：**

项目代码上传成功后，利用USB线上电，你会看到的现象是：1.首先，红灯会亮5秒，然后熄灭；2.其次，黄灯会闪烁3次，然后熄灭；3.然后，绿灯会亮5秒，然后熄灭；4.继续运行上述1-3个步骤。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/88468668fc5f1d198863c79778862194.png)

## 项目06 RGB LED

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/bd43dadde69fa133646046983ca30f0f.png)

**1.项目介绍：**

RGB led由三种颜色(红、绿、蓝)组成，通过混合这三种基本颜色可以发出不同的颜色。在这个项目中，我们将向你介绍RGB LED，并向你展示如何使用ESP32控制RGB LED发出不同的颜色光。即使RGB LED是非常基本的，但这也是一个介绍自己或他人电子和编码基础的伟大方式。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | RGB LED\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*3 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

显示器大多遵循RGB颜色标准，电脑屏幕上的所有颜色都是由红、绿、蓝三种颜色以不同比例混合而成。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/ae51546482ff149a5cd40ead3d110bc8.png)

这个RGB LED有4个引脚，每个颜色(红，绿，蓝)和一个共同的阴极。为了改变RGB led的亮度，我们可以使用ESP的PWM引脚。PWM引脚会给RGB led不同占空比的信号以获得不同的颜色。

如果我们使用3个10位PWM来控制RGBLED，理论上我们可以通过不同的组合创建2^10 ×2^10 ×2^10= 1,073,741,824(10亿)种颜色。

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a89020210f9ff33ca99ddf347cc86a21.png)

**5.项目代码：**

```js
//**********************************************************************
/*
 * 文件名  : RGB LED
 * 描述 : 使用RGB 显示随机颜色.
*/
int ledPins[] = {0, 2, 15};    //定义红、绿、蓝引脚
const byte chns[] = {0, 1, 2};        //定义PWM通道
int red, green, blue;
void setup() {
  for (int i = 0; i < 3; i++) {   //设置pwm通道，1KHz,8bit
    ledcSetup(chns[i], 1000, 8);
    ledcAttachPin(ledPins[i], chns[i]);
  }
}

void loop() {
  red = random(0, 256);
  green = random(0, 256);
  blue = random(0, 256);
  setColor(red, green, blue);
  delay(200);
}

void setColor(byte r, byte g, byte b) {
  ledcWrite(chns[0], 255 - r); //共阴极LED，高电平开启LED.
  ledcWrite(chns[1], 255 - g);
  ledcWrite(chns[2], 255 - b);
}
//*************************************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电后，你会看到的现象是：RGB LED开始显示随机颜色。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b926527c01e66d984f86facdb97e10fc.png)

## 项目07 流水灯

**1.项目介绍：**

在日常生活中，我们可以看到许多由不同颜色的led组成的广告牌。他们不断地改变灯光(像流水一样)来吸引顾客的注意。在这个项目中，我们将使用ESP32控制10个leds实现流水的效果。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 红色LED\*10 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*10 | 跳线若干 | USB 线\*1 |

**3.项目接线图:**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/5a5c21da428df050a30ce9947810486a.png)

**4.项目代码：**

本项目是设计制作一个流水灯。这是这些行动：首先打开LED #1，然后关闭它。然后打开LED #2，然后关闭…并对所有10个LED重复同样的操作，直到最后一个LED关闭。这一过程反复进行，以实现流水的“运动”。

```js
//**********************************************************************
/* 
 * 文件名 : 流水灯
 * 描述 : 用十个led来演示流动灯
*/
byte ledPins[] = {22, 21, 19, 18, 17, 16, 4, 0, 2, 15};
int ledCounts;

void setup() {
  ledCounts = sizeof(ledPins);
  for (int i = 0; i < ledCounts; i++) {
    pinMode(ledPins[i], OUTPUT);
  }
}

void loop() {
  for (int i = 0; i < ledCounts; i++) {
    digitalWrite(ledPins[i], HIGH);
    delay(100);
    digitalWrite(ledPins[i], LOW);
  }
  for (int i = ledCounts - 1; i > -1; i--) {
    digitalWrite(ledPins[i], HIGH);
    delay(100);
    digitalWrite(ledPins[i], LOW);
  }
}
//**********************************************************************
```

**5.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电后，你会看到的现象是：10个LED将从左到右点亮，然后从右到左返回。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7546b1e47be75d184578949aba70ee0a.png)

## 项目08 一位数码管

**1.项目介绍：**

七段数码管是一种显示十进制数字的电子显示设备，广泛应用于数字时钟、电子仪表、基本计算器和其他显示数字信息的电子设备。甚至我们在电影中看到的炸弹也有七段数码管。也许七段数码管看起来不够现代，但它们是更复杂的点阵显示器的替代品，在有限的光线条件下和强烈的阳光下都很容易使用。在这个项目中，我们将使用ESP32控制一位数码管显示数字。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/205aad52163781b8f1857212f1011848.png) |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 一位数码管\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*8 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/24cd9e937ce10d6bd7bd04581d758894.png)

**一位数码管显示原理：** 数码管显示是一种半导体发光器件。它的基本单元是一个发光二极管(LED)。数码管显示根据段数可分为7段数码管和8段数码管。8段数码管比7段多一个LED单元(用于小数点显示)。七段LED显示屏的每段是一个单独的LED。根据LED单元接线方式，数码管可分为共阳极数码管和共阴极什数码管。

在共阴极7段数码管中，分段LED的所有阴极(或负极)都连接在一起，你应该把共阴极连接到GND，要点亮一个分段LED，你可以将其关联的引脚设置为HIGH。

在共阳极7段数码管中，所有段的LED阳极(正极)都连接在一起，你应该把共阳极连接到+5V。要点亮一个分段LED，你可以将其关联的引脚设置为LOW。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/19ff4c77c2703c262a9cd5295724ae02.png)

数码管的每个部分由一个LED组成。所以当你使用它的时候，你也需要使用一个限流电阻。否则，LED会被烧坏。在这个实验中，我们使用了一个普通的共阴极一位数码管。正如我们上面提到的，你应该将公共阴极连接到GND。要点亮一个分段LED，你可以将其关联的引脚设置为HIGH。

**4.项目接线图：**

注意：插入面包板的七段数码管方向与接线图一致，右下角多一个点。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b1774fdf03e96a8b6a16d03db21a93a0.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/da76f2ea8684ac4bb13d8b55d272977a.png)

**5.项目代码：**

数字显示分7段，小数点显示分1段。当显示某些数字时，相应的段将被点亮。例如，当显示数字1时，b和c段将被打开。

```js
//**********************************************************************
/* 
 * 文件名 : 一位数码管
 * 描述 : 一位数管显示从9到0的数字.
*/
// 设置每个段的IO PIN
int a=16; // 段a的数字PIN 16
int b=4; // 段b的数字PIN 4
int c=5; // 段c的数字PIN 5
int d=18; // 段d的数字PIN 18
int e=19; // 段e的数字PIN 19
int f=22; // 段f的数字PIN 22
int g=23; // 段g的数字PIN 23
int dp=17; // 段dp的数字PIN 17
void digital_0(void) // 显示数字0
{
digitalWrite(a,HIGH);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,HIGH);
digitalWrite(e,HIGH);
digitalWrite(f,HIGH);
digitalWrite(g,LOW);
digitalWrite(dp,LOW);
}
void digital_1(void) // 显示数字 1
{
digitalWrite(a,LOW);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,LOW);
digitalWrite(e,LOW);
digitalWrite(f,LOW);
digitalWrite(g,LOW);
digitalWrite(dp,LOW);
}
void digital_2(void) // 显示数字 2
{
digitalWrite(a,HIGH);
digitalWrite(b,HIGH);
digitalWrite(c,LOW);
digitalWrite(d,HIGH);
digitalWrite(e,HIGH);
digitalWrite(f,LOW);
digitalWrite(g,HIGH);
digitalWrite(dp,LOW);
}
void digital_3(void) // 显示数字 3
{
digitalWrite(a,HIGH);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,HIGH);
digitalWrite(f,LOW);
digitalWrite(e,LOW);
digitalWrite(dp,LOW);
digitalWrite(g,HIGH);
}
void digital_4(void) // 显示数字 4
{
digitalWrite(a,LOW);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,LOW);
digitalWrite(e,LOW);
digitalWrite(f,HIGH);
digitalWrite(g,HIGH);
digitalWrite(dp,LOW);
}
void digital_5(void) // 显示数字 5
{
digitalWrite(a,HIGH);
digitalWrite(b,LOW);
digitalWrite(c,HIGH);
digitalWrite(d,HIGH);
digitalWrite(e,LOW);
digitalWrite(f,HIGH);
digitalWrite(g,HIGH);
digitalWrite(dp,LOW);
}
void digital_6(void) // 显示数字 6
{
digitalWrite(a,HIGH);
digitalWrite(b,LOW);
digitalWrite(c,HIGH);
digitalWrite(d,HIGH);
digitalWrite(e,HIGH);
digitalWrite(f,HIGH);
digitalWrite(g,HIGH);
digitalWrite(dp,LOW);
}
void digital_7(void) // 显示数字 7
{
digitalWrite(a,HIGH);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,LOW);
digitalWrite(e,LOW);
digitalWrite(f,LOW);
digitalWrite(g,LOW);
digitalWrite(dp,LOW);
}
void digital_8(void) // 显示数字 8
{
digitalWrite(a,HIGH);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,HIGH);
digitalWrite(e,HIGH);
digitalWrite(f,HIGH);
digitalWrite(g,HIGH);
digitalWrite(dp,LOW);
}
void digital_9(void) // 显示数字 9
{
digitalWrite(a,HIGH);
digitalWrite(b,HIGH);
digitalWrite(c,HIGH);
digitalWrite(d,HIGH);
digitalWrite(e,LOW);
digitalWrite(f,HIGH);
digitalWrite(g,HIGH);
digitalWrite(dp,LOW);
}
void setup()
{
  // 初始化数字引脚LED作为输出.
  pinMode(a, OUTPUT);
  pinMode(b, OUTPUT);
  pinMode(c, OUTPUT);
  pinMode(d, OUTPUT);
  pinMode(e, OUTPUT);
  pinMode(f, OUTPUT);
  pinMode(g, OUTPUT);
  pinMode(dp, OUTPUT);
}
void loop()
{
while(1)
{
digital_9();// 显示数字 9
delay(1000); // 延时 1 s
digital_8();// 显示数字 8
delay(1000); 
digital_7();// 显示数字 7
delay(1000); 
digital_6();// 显示数字 6
delay(1000); 
digital_5();// 显示数字 5
delay(1000); 
digital_4();// 显示数字 4
delay(1000); 
digital_3();// 显示数字 3
delay(1000); 
digital_2();// 显示数字 2
delay(1000);
digital_1();// 显示数字 1
delay(1000);
digital_0();// 显示数字 0
delay(1000);
}}
//**********************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电后，你会看到的现象是：一位数码管将显示从9到0的数字。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/06886089efa6a52fef3a5e85cd3327cc.png)

## 项目09 四位数码管

**1.项目介绍：**

四位数码管是一种非常实用的显示器件，电子时钟的显示，球场上的记分员，公园里的人数都是需要的。由于价格低廉，使用方便，越来越多的项目将使用4位数码管。在这个项目中，我们使用ESP32控制四位数码管来显示数字。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9fdfea69b62531a92309052760130694.png) |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 四位数码管\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*8 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/57fbc9480875664d1ad27536ff79728d.png)

**四位数码管：** 四位数码管有共阳极和共阴极两种四位数码管，显示原理是和一位数码管是类似的，都是8个GPIO口控制数码管的显示段，就是8个led灯，不过，这里是4位的，所以就还需要4个GPIO口来控制位选择端，就是选择哪个单个数码管亮，位的切换很快，肉眼区分不出来，这样看起来是多个数码管同时显示。

我们的四位数码管是共阴极的。

下图为4位数码管的引脚图，G1、G2、G3、G4就是控制位的引脚。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/da1ad5bb24e7bcd8b2671641ed38f3c4.png)

下图为4位数码管内部布线原理图

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2b44ecde85d4f9fcaa73cfcd2e6914d9.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/84fbe7aa78a614af5e5e8ada6c7a509d.png)

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b34ecf777f13629448cd300a8c33e39c.png)

**5.项目代码：**

```js
//**********************************************************************
/* 
 * 文件名  : 四位数码管
 * 描述 : 四位数管显示从0到9999的数字。
*/
#define d_a 18   //定义数码管a至引脚18
#define d_b 13
#define d_c 2
#define d_d 16
#define d_e 17
#define d_f 19
#define d_g 0
#define d_dp 4

#define G1 21   //定义第一组nixtube G1到引脚21
#define G2 22
#define G3 14
#define G4 15

//数码管0-F码值
unsigned char num[17][8] =
{
 //a  b  c  d  e  f  g  dp 
  {1, 1, 1, 1, 1, 1, 0, 0},     //0
  {0, 1, 1, 0, 0, 0, 0, 0},     //1
  {1, 1, 0, 1, 1, 0, 1, 0},     //2
  {1, 1, 1, 1, 0, 0, 1, 0},     //3
  {0, 1, 1, 0, 0, 1, 1, 0},     //4
  {1, 0, 1, 1, 0, 1, 1, 0},     //5
  {1, 0, 1, 1, 1, 1, 1, 0},     //6
  {1, 1, 1, 0, 0, 0, 0, 0},     //7
  {1, 1, 1, 1, 1, 1, 1, 0},     //8
  {1, 1, 1, 1, 0, 1, 1, 0},     //9
  {1, 1, 1, 0, 1, 1, 1, 1},     //A
  {1, 1, 1, 1, 1, 1, 1, 1},     //B
  {1, 0, 0, 1, 1, 1, 0, 1},     //C
  {1, 1, 1, 1, 1, 1, 0, 1},     //D
  {1, 0, 0, 1, 1, 1, 1, 1},     //E
  {1, 0, 0, 0, 1, 1, 1, 1},     //F
  {0, 0, 0, 0, 0, 0, 0, 1},     //.
};

void setup()
{
  pinMode(d_a,OUTPUT);    //设置为输出引脚
  pinMode(d_b,OUTPUT);
  pinMode(d_c,OUTPUT);
  pinMode(d_d,OUTPUT);
  pinMode(d_e,OUTPUT);
  pinMode(d_f,OUTPUT);
  pinMode(d_g,OUTPUT);
  pinMode(d_dp,OUTPUT);

  pinMode(G1,OUTPUT);
  pinMode(G2,OUTPUT);
  pinMode(G3,OUTPUT);
  pinMode(G4,OUTPUT);
}

void loop()
{

  //从0开始计数，逐渐增加1到9999，重复。
  for(int l = 0;l < 10;l++ )
  {
    for(int k = 0; k < 10;k++)
    {
      for(int j = 0; j < 10; j++)
      {
        for(int i = 0;i < 10;i++)
        {
          //每秒闪烁125次等于1秒.
          //1000/8=125
          for(int q = 0;q<125;q++)
          {
            Display(1,l);//第一个数码管显示了l的值。
            delay(2);
            Display(2,k);
            delay(2);
            Display(3,j);
            delay(2);
            Display(4,i);
            delay(2);
          }

        }
      }
    }
  }

}

//显示功能:g的取值范围是1 ~ 4,num的取值范围是0 ~ 9
void Display(unsigned char g,unsigned char n) 
{
  digitalWrite(d_a,LOW);      //关闭对应LED段
  digitalWrite(d_b,LOW);
  digitalWrite(d_c,LOW);
  digitalWrite(d_d,LOW);
  digitalWrite(d_e,LOW);
  digitalWrite(d_f,LOW);
  digitalWrite(d_g,LOW);
  digitalWrite(d_dp,LOW);

  switch(g)           //选择
  {
    case 1:
      digitalWrite(G1,LOW);   //选择第一个数字
      digitalWrite(G2,HIGH);
      digitalWrite(G3,HIGH);
      digitalWrite(G4,HIGH);
      break;
    case 2:
      digitalWrite(G1,HIGH);
      digitalWrite(G2,LOW);   //选择第二个数字
      digitalWrite(G3,HIGH);
      digitalWrite(G4,HIGH);
      break;
    case 3:
      digitalWrite(G1,HIGH);
      digitalWrite(G2,HIGH);
      digitalWrite(G3,LOW);   //选择第三个数字
      digitalWrite(G4,HIGH);
      break;
    case 4:
      digitalWrite(G1,HIGH);
      digitalWrite(G2,HIGH);
      digitalWrite(G3,HIGH);
      digitalWrite(G4,LOW);   //选择第四个数字
      break;
    default:break;
  }

  digitalWrite(d_a,num[n][0]);      //a查询码值表
  digitalWrite(d_b,num[n][1]);
  digitalWrite(d_c,num[n][2]);
  digitalWrite(d_d,num[n][3]);
  digitalWrite(d_e,num[n][4]);
  digitalWrite(d_f,num[n][5]);
  digitalWrite(d_g,num[n][6]);
  digitalWrite(d_dp,num[n][7]);
}
//**********************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电后，你会看到的现象是：四位数码管从0000开始计数，逐渐增加1到9999，并在一个无限循环中重复这些动作。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e1420c7260ab37cc391a3849cb200d51.png)

## 项目10 8×8点阵屏

**1.项目介绍：**

点阵屏是一种电子数字显示设备，可以显示机器、钟表、公共交通离场指示器和许多其他设备上的信息。在这个项目中，我们将使用ESP32控制8x8 LED点阵来显示图案。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/e5d2b82a75e728b09b97cc15056b0287.png) |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 8×8点阵屏\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 220Ω电阻\*8 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

**8×8点阵：** 是由64个led灯组成，有行共阳极和行共阴极两种，我们的模块是行共阳极的，也就是每一行有一条线将LED的正极连到一起，列就是将LED灯的负极连接到一起，看下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c41aaa44085da33d18ef06727b575091.png)

每个LED被放置在一行和一列的交叉点上。当某一行的电平为1，某列的电平为0时，对应的LED会亮起。如果你想点亮第一个点上的LED，你应该将引脚9设置为高电平，引脚13设置为低电平。如果你想点亮第一排的led，你应该把引脚9设置为高电平，把引脚13、3、4、10、6、11、15和16设置为低电平。如果您想点亮第一列的led，将引脚13设置为低电平，将引脚9、14、8、12、1、7、2和5设置为高电平。

**8×8点阵屏的外部视图如下所示：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/fec2ed85eb857e75ef5f3692fa6a4325.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1c0b9e68ffa8d605e0b4ee1b36e07d57.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/086139dd80ed8bc7b7f291357ec94f4f.png)

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/38b8f66bb31399c80d56e4bed07b3803.png)

**5.项目代码：**

```js
//**********************************************************************************
/* 
 * 文件名  : 8×8点阵屏
 * 描述 : 8×8点阵显示从0到9的数字.
*/
int R[] = {14,26,4,27,19,16,18,17};
int C[] = {32,21,22,12,0,13,33,25};

unsigned char data_0[8][8] =
{
{0,0,1,1,1,0,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,0,1,1,1,0,0,0}
};

unsigned char data_1[8][8] =
{
{0,0,0,0,1,0,0,0},
{0,0,0,1,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,1,1,1,0,0}
};

unsigned char data_2[8][8] =
{
{0,0,1,1,1,0,0,0},
{0,1,0,0,0,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,1,0,0,0,0},
{0,0,1,0,0,0,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_3[8][8] =
{
{0,0,1,1,1,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,1,1,1,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,1,1,1,1,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_4[8][8] =
{
{0,1,0,0,0,0,0,0},
{0,1,0,0,1,0,0,0},
{0,1,0,0,1,0,0,0},
{0,1,1,1,1,1,1,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_5[8][8] =
{
{0,1,0,0,0,0,0,0},
{0,1,1,1,1,1,0,0},
{0,1,0,0,0,0,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,0,0,0,1,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_6[8][8] =
{
{0,1,1,1,1,1,0,0},
{0,1,0,0,0,0,0,0},
{0,1,0,0,0,0,0,0},
{0,1,1,1,1,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_7[8][8] =
{
{0,0,0,0,0,0,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,0,0,1,0,0,0},
{0,0,0,1,0,0,0,0},
{0,0,1,0,0,0,0,0},
{0,1,0,0,0,0,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_8[8][8] =
{
{0,1,1,1,1,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,1,1,1,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,0,0,0}
};

unsigned char data_9[8][8] =
{
{0,1,1,1,1,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,0,0,0,1,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,1,0,0},
{0,0,0,0,0,1,0,0},
{0,1,1,1,1,1,0,0},
{0,0,0,0,0,0,0,0}
};

void Display(unsigned char dat[8][8])
{
for(int c = 0; c<8;c++)
{
digitalWrite(C[c],LOW);
for(int r = 0;r<8;r++)
{
digitalWrite(R[r],dat[r][c]);
}
delay(1);
Clear();
}
}

void Clear()
{
for(int i = 0;i<8;i++)
{
digitalWrite(R[i],LOW);
digitalWrite(C[i],HIGH);
}
}

void setup(){
  for(int i = 0;i<8;i++)
  {
    pinMode(R[i],OUTPUT);
    pinMode(C[i],OUTPUT);
  }

}

void loop(){
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_0);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_1);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_2);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_3);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_4);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_5);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_6);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_7);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_8);
  }
  for (int i = 1; i <= 100; i = i + (1)) {
    Display(data_9);
  }
}
//**********************************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：8\*8点阵屏依次显示数字0~9，循环进行。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d979f0b9a2cd828104fa2155632fe690.png)

## 项目11 74HC595N控制8个LED

**1.项目介绍：**

在之前的项目中，我们已经学过了怎样点亮一个LED。

ESP32上只有32个IO端口，我们如何点亮大量的led呢? 有时可能会耗尽ESP32上的所有引脚，这时候就需要用移位寄存器扩展它。你可以使用74HC595N芯片一次控制8个输出，而只占用你的微控制器上的几个引脚。你还可以将多个寄存器连接在一起，以进一步扩展输出，在这个项目中，我们将使用ESP32，74HC595芯片和LED制作一个流水灯来了解74HC595芯片的功能。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/44e4bc7bbc88eafd59a57fc4876f6725.png) |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 74HC595N芯片\*1 | 红色LED\*8 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |  |
| 220Ω电阻\*8 | 跳线若干 | USB 线\*1 |  |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/46bf6b6fb5968bea5b7fd226a6f95567.png)

**74HC595N芯片：** 简单来说就是具有8 位移位寄存器和一个存储器，以及三态输出功能。移位寄存器和存储器同步于不同的时钟，数据在移位寄存器时钟SCK的上升沿输入，在存储寄存器时钟RCK的上升沿进入的存储寄存器中去。如果两个时钟连在一起，则移位寄存器总是比存储寄存器早一个脉冲。移位寄存器有一个串行移位输入端（SI）和一个用于级联的串行输出端（SQH）,8位移位寄存器可以异步复位（低电平复位），存储寄存器有一个8位三态并行的总线输出，当输出使能（OE）被使能（低电平有效）将存储寄存器中输出至74HC595N的引脚（总线）。

![Img](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/media/img-20241115082324.png)

**引脚说明：**

| 引脚： | 引脚说明： |
| --- | --- |
| 13引脚OE | 是一个输出使能引脚，用于确保锁存器的数据是否输入到Q0-Q7引脚。在低电平时，不输出高电平。在本实验中，我们直接连接GND，保持低电平输出数据。 |
| 14引脚SI/DS | 这是74HC595接收数据的引脚，即串行数据输入端，一次只能输入一位，那么连续输入8次，就可以组成一个字节了。 |
| 10引脚SCLR/MR | 一个初始化存储寄存器管脚的管脚。在低电平时初始化内部存储寄存器。在这个实验中，我们连接VCC以保持高水平。 |
| 11引脚SCK/SH\_CP | 移位寄存器的时钟引脚，上升沿时，移位寄存器中的数据整体后移，并接收新的数据输入。 |
| 12引脚RCK/ST\_CP | 存储寄存器的时钟输入引脚。上升沿时，数据从移位寄存器转存到存储寄存器中。这时数据就从Q0~Q7端口并行输出。 |
| 9引脚SQH | 引脚是一个串行输出引脚，专门用于芯片级联，接下一个74HC595的SI端。 |
| Q0--Q7(15引脚，1-7引脚) | 八位并行输出端，可以直接控制数码管的8个段。 |

**4.项目接线图：**

注意：需要注意74HC595N芯片插入的方向。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9b0649bf40ed46557baabcf617c69804.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cc4e68fb791ca0f40d0e6554d43b1ab2.png)

**5.项目代码：**

```js
//**********************************************************************
/* 
 * 文件名 : 74HC595N 控制8个LED
 * 描述 : 用74HC575N驱动十个led流水灯.
*/
int dataPin = 14;   // 连接74HC595 DS的引脚(Pin14)  
int latchPin = 12;  // 74HC595的ST_CP连接引脚(Pin12)
int clockPin = 13;  // 74HC595的SH_CP连接引脚(Pin11)          

void setup() {
  // 设置引脚输出
  pinMode(latchPin, OUTPUT);
  pinMode(clockPin, OUTPUT);
  pinMode(dataPin, OUTPUT);
}

void loop() {
  // 定义一个单字节变量，用8位来表示LED柱状图的8个LED的状态。
  //该变量被赋值为0x01，即二进制00000001，表示只有一盏灯亮。
  byte x = 0x01;    // 0b 0000 0001
  for (int j = 0; j < 8; j++) { // 让led灯从右向左亮起
    writeTo595(LSBFIRST, x);
    x <<= 1; // 使变量向左移动一位，然后明亮的LED向左移动一步.
    delay(50);
  }
  delay(100);
  x = 0x80;       //0b 1000 0000
  for (int j = 0; j < 8; j++) { // 让led灯从左向右亮起
    writeTo595(LSBFIRST, x);
    x >>= 1;    
    delay(50);
  }
  delay(100);
}
void writeTo595(int order, byte _data ) {
  // 输出低电平到latchPin
  digitalWrite(latchPin, LOW);
  // 发送串行数据到74HC595
  shiftOut(dataPin, clockPin, order, _data);
  // 输出高电平到latchPin，74HC595将数据更新到并行输出端口.
  digitalWrite(latchPin, HIGH);
}
//**********************************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，可以看到的现象是：8个LED开始以流水模式闪烁。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/de7ef1d58423282ec05b12fdc0af0292.png)

## 项目12 有源蜂鸣器

**1.项目介绍：**

有源蜂鸣器是一个发声组件。它被广泛用作电脑、打印机、报警器、电子玩具、电话、计时器等的发声元件。它有一个内在的振动源，只需连接5V电源，即可持续发出嗡嗡声。在这个项目中，我们将使用ESP32控制有源蜂鸣器发出“滴滴”声。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a802ce7dc086858873e587c19b45578d.png) |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 有源蜂鸣器\*1 | NPN型晶体管(S8050)\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |  |
| 1KΩ电阻\*1 | 跳线若干 | USB线\*1 |  |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7ac473b007da81c891ca12b4c82fac4a.png)

**有源蜂鸣器：** 它内部有一个简单的振荡器电路，可以将恒定的直流电转换成特定频率的脉冲信号。一旦有源蜂鸣器收到一个高电平，它将产生声音。而无源蜂鸣器是一种内部没有振动源的集成电子蜂鸣器，它必须由2K-5K方波驱动，而不是直流信号。这两个蜂鸣器的外观非常相似，但是一个带有绿色电路板的蜂鸣器是无源蜂鸣器，而另一个带有黑色胶带的是有源蜂鸣器。无源蜂鸣器不能区分正极性而有源蜂鸣器是可以，如下所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a33496a4d5eb90ea4d980459d7654b0f.png)

**晶体管:** 由于蜂鸣器需要很大的电流，ESP32输出能力的GPIO不能满足要求，这里需要一个NPN型晶体管来放大电流。晶体管，全称:半导体晶体管，是一种控制电流的半导体器件。晶体管可以用来放大微弱信号，也可以用作开关。它有三个电极(pin)：基极(b)，集电极(c)和发射极(e)。当电流通过“be”之间时，“ce”将允许几倍的电流通过(晶体管放大)，此时，晶体管在放大区工作。当“be”之间的电流超过某个值时，“ce”将不再允许电流增加，此时晶体管工作在饱和区。晶体管有两种类型如下所示：PNP和NPN

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/96deb6c440780b1bc8acde29ef214727.png)

在我们的套件中，PNP晶体管标记为8550,NPN晶体管标记为8050。

基于晶体管的特性，它常被用作数字电路中的开关。由于单片机输出电流的能力很弱，我们将使用晶体管来放大电流和驱动大电流的元件。在使用NPN晶体管驱动蜂鸣器时，通常采用以下方法：如果GPIO输出高电平，电流将流过R1，晶体管将传导，蜂鸣器将发出声音。如果GPIO输出低电平，没有电流流过R1，晶体管就不会传导，蜂鸣器也不会响。在使用PNP晶体管驱动蜂鸣器时，通常采用以下方法：如果GPIO输出低电平，电流将流过R1，晶体管将传导，蜂鸣器将发出声音。如果GPIO输出高电平，没有电流流过R1，晶体管就不会传导，蜂鸣器也不会响。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/05d42ad7919cc237e007cd2457aacd7c.png)

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/45b50e33587ccd6c5c2b5af7c7ba7658.png)

注意：该电路中蜂鸣器的电源为5V。在3.3V的电源下，蜂鸣器可以工作，但会降低响度。

**5.项目代码：**

```js
//**********************************************************************
/* 
 * 文件名   : 有源蜂鸣器
 * 描述 : 有源蜂鸣器鸣叫.
*/
#define buzzerPin  15   //定义蜂鸣器引脚

void setup ()
{
  pinMode (buzzerPin, OUTPUT);
}
void loop ()
{
  digitalWrite (buzzerPin, HIGH);
  delay (500);
  digitalWrite (buzzerPin, LOW);
  delay (500);
}
//**********************************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，可以看到的现象是：有源蜂鸣器发出“滴滴”声。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4d1cee558b3cb395234d05214995c631.png)

## 项目13 无源蜂鸣器

**1.项目介绍：**

在之前的项目中，我们研究了有源蜂鸣器，它只能发出一种声音，可能会让你觉得很单调。这个项目将学习另一种蜂鸣器，无源蜂鸣器。与有源蜂鸣器不同，无源蜂鸣器可以发出不同频率的声音。在这个项目中，你将使用ESP32控制无源蜂鸣器工作。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8b2bc33166824a904168c38b960fe574.png) |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 无源蜂鸣器\*1 | NPN型晶体管(S8050)\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |  |
| 1KΩ电阻\*1 | 跳线若干 | USB线\*1 |  |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/352568cc59e03453c9433f866631bf50.png)

**无源蜂鸣器：** 它是一种内部没有振动源的集成电子蜂鸣器。它必须由2K-5K方波驱动，而不是直流信号。与有源蜂鸣器的外观非常相似，但是一个带有绿色电路板的蜂鸣器是无源蜂鸣器，而另一个带有黑色胶带的是有源蜂鸣器。无源蜂鸣器不能区分正极性而有源蜂鸣器是可以，如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9e2535b132d6c88b45b2b8dfdf6dbda4.png)

**晶体管:** 请参考 **项目12** 。

**4.项目接线图:**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9116a43de7b0f2dc1cdb0496468706f3.png)

**5.项目代码：**

```js
//**********************************************************************
/*
 * 文件名   : 无源蜂鸣器
 * 描述 : 无源蜂鸣器发出警报.
*/
#define LEDC_CHANNEL_0 0

// LEDC定时器采用13位精度

#define LEDC_TIMER_13_BIT  13

// 定义工具I/O端口

#define BUZZER_PIN  15

//创建一个音乐旋律列表

int melody[] = {330, 330, 330, 262, 330, 392, 196, 262, 196, 165, 220, 247, 233, 220, 196, 330, 392, 440, 349, 392, 330, 262, 294, 247, 262, 196, 165, 220, 247, 233, 220, 196, 330, 392,440, 349, 392, 330, 262, 294, 247, 392, 370, 330, 311, 330, 208, 220, 262, 220, 262,

294, 392, 370, 330, 311, 330, 523, 523, 523, 392, 370, 330, 311, 330, 208, 220, 262,220, 262, 294, 311, 294, 262, 262, 262, 262, 262, 294, 330, 262, 220, 196, 262, 262,262, 262, 294, 330, 262, 262, 262, 262, 294, 330, 262, 220, 196};

//创建一个音调持续时间列表

int noteDurations[] = {8,4,4,8,4,2,2,3,3,3,4,4,8,4,8,8,8,4,8,4,3,8,8,3,3,3,3,4,4,8,4,8,8,8,4,8,4,3,8,8,2,8,8,8,4,4,8,8,4,8,8,3,8,8,8,4,4,4,8,2,8,8,8,4,4,8,8,4,8,8,3,3,3,1,8,4,4,8,4,8,4,8,2,8,4,4,8,4,1,8,4,4,8,4,8,4,8,2};
void setup() {
pinMode(BUZZER_PIN, OUTPUT); // 将蜂鸣器设置为输出模式
}

void loop() {

  int noteDuration; //创建一个变量noteDuration

  for (int i = 0; i < sizeof(noteDurations); ++i)

  {
      noteDuration = 800/noteDurations[i];

      ledcSetup(LEDC_CHANNEL_0, melody[i]*2, LEDC_TIMER_13_BIT);

      ledcAttachPin(BUZZER_PIN, LEDC_CHANNEL_0);

      ledcWrite(LEDC_CHANNEL_0, 50);

      delay(noteDuration * 1.30); //延时
  }
}
//**********************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：无源蜂鸣器播放音乐。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/f602628b53d8dd59744d5405b604f8c6.png)

## 项目14 小台灯

**1.项目介绍：**

你知道ESP32可以在你按下外接按键的时候点亮LED吗? 在这个项目中，我们将使用ESP32，一个按键开关和一个LED来制作一个迷你台灯。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4c27c3e6c2224e12fd1bd298669f700c.png) |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 按键\*1 |
|  |  |  |
| 10KΩ电阻\*1 | 红色 LED\*1 | 220Ω电阻\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4214d58466508ada517d241d46485b7b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 按键帽\*1 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/50b8a411811e86cb4204a2e0377282f4.png)

**按键：** 按键可以控制电路的通断，把按键接入电路中，不按下按键的时候电路是断开的，

一按下按键电路就通啦，但是松开之后就又断了。可是为什么按下才通电呢？这得从按键的内部构造说起。没按下之前，电流从按键的一端过不去另一端；按下的时候，按键内部的金属片把两边连接起来让电流通过。

按键内部结构如图： ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/013b1897a0308c71d7e5ed0eaf64198d.png) ，未按下按键之前，1、2就是导通的，3、4也是导通的，但是1、3或1、4或2、3或2、4是断开（不通）的；只有按下按键时，1、3或1、4或2、3或2、4才是导通的。

在设计电路时，按键开关是最常用的一种元件。

**按键的原理图:**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/00096ba2b3cb95b8d02e075261acb181.png)

**什么是按键抖动？**

我们想象的开关电路是“按下按键-立刻导通”“再次按下-立刻断开”，而实际上并非如此。 按键通常采用机械弹性开关，而机械弹性开关在机械触点断开闭合的瞬间（通常 10ms左右），会由于弹性作用产生一系列的抖动，造成按键开关在闭合时不会立刻稳定的接通电路，在断开时也不会瞬时彻底断开。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c4d829daa9d08e80d4538b901c700b8c.png)

**那又如何消除按键抖动呢？**

常用除抖动方法有两种：软件方法和硬件方法。这里重点讲讲方便简单的软件方法。 我们已经知道弹性惯性产生的抖动时间为10ms 左右，用延时命令推迟命令执行的时间就可以达到除抖动的效果。

所以我们在代码中加入了0.02秒的延时以实现按键防抖的功能。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3591ca9171277a78bf782c32525e1812.png)

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/79c310407b3091e58739fda1c916f412.png)

注意:

怎样连接LED

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cbb16ef4d8cb62a4001d1a05ae3ac615.png)

怎样识别五色环220Ω电阻和五色环10KΩ电阻

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/951ce7d7778b34bf8fbdb3de1b8c3116.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2d3f0996515352c92c07d17e074e7308.png)

**5.项目代码：**

```js
//**********************************************************************
/* 
 * 文件名  : 小台灯
 * 描述 : 做一个小台灯.
*/
#define PIN_LED    4
#define PIN_BUTTON 15
bool ledState = false;

void setup() {
  // 初始化数字引脚PIN_LED作为输出.
  pinMode(PIN_LED, OUTPUT);
  pinMode(PIN_BUTTON, INPUT);
}

// 循环函数永远重复运行
void loop() {
  if (digitalRead(PIN_BUTTON) == LOW) {
    delay(20);
    if (digitalRead(PIN_BUTTON) == LOW) {
      reverseGPIO(PIN_LED);
    }
    while (digitalRead(PIN_BUTTON) == LOW);
  }
}

void reverseGPIO(int pin) {
  ledState = !ledState;
  digitalWrite(pin, ledState);
}
//**********************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：按下按钮，LED亮起；当按钮松开时，LED仍亮着。再次按下按钮，LED熄灭；当按钮释放时，LED保持关闭。是不是很像个小台灯？

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cba65f3e78fc23732983efae1f0441e4.png)

## 项目15 模拟沙漏

**1.项目介绍：**

古代人没有电子时钟，就发明了沙漏来测时间，沙漏两边的容量比较大，在一边装了细沙，中间有个很小的通道，将沙漏直立，有细沙的一边在上方，由于重力的作用，细沙就会往下流通过通道到沙漏的另一边，当细沙都流到下边了，就倒过来，把一天反复的次数记录下来，第二天就可以通过沙漏反复流动的次数而知道这一天大概的时间了。这一课我们将利用ESP32控制倾斜开关和LED灯来模拟沙漏，制作一个电子沙漏。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/01c1502f7cc76c09d4d77410dcdd31a6.png) |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 倾斜开关\*1 | 10KΩ电阻\*1 |
|  |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |
| 红色 LED\*4 | 220Ω电阻\*1 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/966683cc81a185103df1862a16d7a844.png)

倾斜开关也叫数字开关或球形开关，里面有一个金属球。它用于检测小角度的倾斜。

原理很简单：当开关倾斜一定角度时，里面的球会向下滚动，接触到连接到外面引脚的两个触点，从而触发电路。否则，球将远离触点，从而断开电路。

这里用倾斜开关的内部结构来说明它是如何工作的，显示如下图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/ed8fb48d5fbce7033fad9ffa5de67808.png)

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/02d5f7df75aa8062f7b9399a47ae4285.png)

注意:

怎样连接LED

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cbb16ef4d8cb62a4001d1a05ae3ac615.png)

怎样识别五色环220Ω电阻和五色环10KΩ电阻

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/951ce7d7778b34bf8fbdb3de1b8c3116.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2d3f0996515352c92c07d17e074e7308.png)

**5.项目代码：**

```js
//**********************************************************************
/* 
 * 文件名  : 倾斜和LED
 * 描述 : 倾斜开关和四个led模拟沙漏.
*/
#define SWITCH_PIN  15  // 倾斜开关连接Pin15
byte switch_state = 0;
void setup()
{
     for(int i=16;i<20;i++)
  {
        pinMode(i, OUTPUT);
  } 
    pinMode(SWITCH_PIN, INPUT);
 for(int i=16;i<20;i++)
  {
    digitalWrite(i,0);
  } 
  Serial.begin(9600);
}
void loop()
{
switch_state = digitalRead(SWITCH_PIN); 
Serial.println(switch_state);
 if (switch_state == 0) 
 {
 for(int i=16;i<20;i++)
  {
    digitalWrite(i,1);
    delay(500);
  } 
  }
   if (switch_state == 1) 
 {
   for(int i=19;i>15;i--)
   {
    digitalWrite(i,0);
    delay(500);
   }
  }
}
//**********************************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：将面包板倾斜到一定角度，led就会一个一个地亮起来。当回到上一个角度时，led会一个一个关闭。就像沙漏一样，随着时间的推移，沙子漏了出来。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2db9ee9bf59debc53c46aa954da31d47.png)

## 项目16 I2C 128×32 LCD

**1.项目介绍：**

在生活中，我们可以利用显示器等模块来做各种实验。你也可以DIY各种各样的小物件。例如，用一个温度传感器和显示器做一个温度测试仪，或者用一个超声波模块和显示器做一个距离测试仪。下面，我们将使用LCD\_128X32\_DOT模块作为显示器，将其连接到ESP32控制板上。将使用ESP32主板控制LCD\_128X32\_DOT显示屏显示各种英文文字、常用符号和数字。

**注意：配件我们已将LCD屏升级为更常用的OLED屏幕，文档陆续更新中，如发现代码运行错误请联系客服修复。**

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c4b08ad87310ea27bc06383257cc7f8f.png) |
| --- | --- | --- |
| ESP32\*1 | 面包板\*1 | OLED屏\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/25a2a8622647c1f1b576468786b0a762.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |  |
| 公对母杜邦线\*4 | USB 线\*1 |  |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c4b08ad87310ea27bc06383257cc7f8f.png)

**LCD\_128X32\_DOT：** 一个像素为128\*32的液晶屏模块，它的驱动芯片为ST7567A。模块使用IIC通信方式，它不仅可以显示英文字母、符号，还可以显示中文文字和图案。使用时，还可以在代码中设置，让英文字母和符号等显示不同大小。

**LCD\_128X32\_DOT原理图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a6f56f3c6481d37dc0dcf9cf74587781.png)

**LCD\_128X32\_DOT技术参数：**

显示像素：128\*32 字符

工作电压：DC 5V

工作电流：100mA (5V)

模块最佳工作电压：5V

亮度、对比度可通过程序指令控制

**4.项目接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/229ce4e1ad8b01c71577d8f777e8195b.png)

**5.添加LCD\_128×32库：**

本项目代码使用了“Adafruit\_SSD1306”和“Adafruit\_GFX”库。如果你还没有添加，请在学习之前安装它。添加第三方库的步骤如下:

打开Arduino IDE，单击“ **左侧的库管理图标** ”。在搜索框中分别输入 **Adafruit\_SSD1306** 和 **Adafruit\_GFX** ，单击“ **安装** ”。

![图片不存在](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0a8f5aa82bea27a65f8455e9a180de96.png)

**6.项目代码：**

```js
//**********************************************************************************
/*
 * 文件名  : LCD 128*32
 * 描述 : LCD 128*32显示字符串
*/
//**********************************************************************************
/*
 * 文件名  : LCD 128*32
 * 描述 : LCD 128*32显示字符串
*/
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  Serial.begin(9600);

  // 初始化I2C，显示屏引脚为SDA=21, SCL或SCK=22
  Wire.begin(21, 22);

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("初始化失败");
    return;
  }

  Serial.println("OLED初始化成功");

  display.clearDisplay();
  display.setTextSize(1);  // 设置字体大小
  display.setTextColor(SSD1306_WHITE); // 设置文字颜色（白色）
  display.setCursor(0, 0); // 设置光标位置 (x, y)

  display.println("Hello world");
  display.println("0123456789");
  display.println("Using SoftI2C");
  display.display();

  Serial.println("显示内容已更新");
}

void loop() {
  // 可以在这里添加你想显示的内容
  delay(1000);
}
//**********************************************************************************
```

**7.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：128X32LCD模块显示屏第一行显示“Hello World!”、第二行显示“0123456789”、第三行显示“Using SoftI2C”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/eeada568b7ef98fefa27e9f22435befd.png)

## 项目17 小风扇

**1.项目介绍：**

在炎热的夏季，需要电扇来给我们降温，那么在这个项目中，我们将使用ESP32控制直流电机和小扇叶来制作一个小电扇。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/dae45d195fded43701882b0f36f21b98.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c562656c96657983e724daed7c8d6202.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3cc5c242c0f357b3dc496037a7d8bae8.png) |
| --- | --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 直流电机\*1 | 面包板专用电源模块\*1 | 6节5号电池盒\*1 |
|  |  |  |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/ed228ff5627e0c8cc0c79fa04f1d4d16.png) |
| NPN型晶体管(S8050)\*1 | PNP型晶体管(S8550)\*1 | 1KΩ 电阻\*1 | 二极管\*1 | 三叶软桨\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |  |  |
| 跳线若干 | 5号电池(自备)\*6 | USB 线\*1 |  |  |

**3.元件知识:**

**二极管：** 它是用半导体材料(硅、硒、锗等)制成的一种电子器件。它有两个电极，正极，又叫阳极；负极，又叫阴极，给二极管两极间加上正向电压时，二极管导通， 加上反向电压时，二极管截止。 二极管的导通和截止，则相当于开关的接通与断开。

灰色圈一端为负极(**\-**)，则另一端为正极(**+**)，如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1a0eeedb91bbad682badb3f1884ef050.png)

二极管具有单向导电性能，导通时电流方向是由阳极通过管子流向阴极。

**面包板专用电源模块：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c562656c96657983e724daed7c8d6202.png)

**说明：**

此模块，能方便的给面包板提供3.3V和5V的电源，具有DC2.1输入（DC7－12V），另外，具备USB Type C接口的电源输入。

**规格：**

输入电压：DC座：7-12V； Type C USB：5V

电流：3.3V：最大500mA； 5V：最大500mA；

最大功率: 2.5W

尺寸: 53mmx26.3mm

环保属性: ROHS

**接口说明：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9ab1fa7ff5eccecdc0fe6218fad6df79.png)

**电机接线说明：**

因现批次电机采用了标准接口导致无法直接插入面包板，请用两根杜邦线插入白色接口处完成本次实验。

![图片不存在](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/dianjijiexiantu.png)

**原理图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/5139ee652d196f215b53c35d8ab24166.png)

**4\. 项目接线图1：**

（这个实验是使用S8050（NPN型晶体管）控制电机）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/bff2df6024667c53eee4f26a7b074911.png)

(注: 先接好线，然后在直流电机上安装一个小风扇叶片。)

**5\. 项目代码1：**

```js
//**********************************************************************
/* 
 * 文件名  :  小风扇
 * 描述 : S8050三极管驱动电机工作
*/

void setup() {

  pinMode(15, OUTPUT); // 初始化引脚15作为输出
}

void loop() {
  digitalWrite(15, HIGH);   // 打开电机(HIGH表示高电平)
  delay(4000);              // 延时 4 秒
  digitalWrite(15, LOW);    // 降低电压，关掉电机
  delay(2000);              // 延时 2 秒
}
//**********************************************************************************
```

**6\. 项目现象1：**

编译并上传代码到ESP32，代码上传成功后，外接电源，上电后，你会看到的现象是：电机转动4秒，停止2秒，以此规律重复执行。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3fa9f68a6dae4cc181e84774f7ba3db4.png)

**7\. 项目接线图2：**

（这个实验是使用S8550（PNP型晶体管）控制电机）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/48132a2a51ed288bd3329358690883b3.png)

(注: 先接好线，然后在直流电机上安装一个小风扇叶片。)

**8\. 项目代码2：**

```js
//**********************************************************************
/* 
 * 文件名  :  小风扇
 * 描述 : S8550三极管驱动电机工作
*/

void setup() {

  pinMode(15, OUTPUT); // 初始化引脚15作为输出
}

void loop() {
  digitalWrite(15, LOW);   // 打开电机(LOW 表示低电平)
  delay(4000);              // 延时4秒
  digitalWrite(15, HIGH);    // 升高电压，关掉电机
  delay(2000);              // 延时2秒
}
//**********************************************************************************
```

**9\. 项目现象2：**

编译并上传代码到ESP32，代码上传成功后，外接电源，上电后，你会看到的现象是：电机转动4秒，停止2秒，以此规律重复执行。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8fc203e455a610033b39ef535e470c6c.png)

## 项目18 调光灯

**1.项目介绍：**

电位器是一个带有滑动或旋转触点的三端电阻器，它形成一个可调的分压器。它的工作原理是在均匀电阻上改变滑动触点的位置。在电位器中，整个输入电压被施加到电阻的整个长度上，输出电压是固定触点和滑动触点之间的电压值。在这个项目中，我们将学习使用ESP32读取电位器的值，并结合LED制作一个调光灯。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/75c2400a88559cf22295c22147625d98.png) |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 可调电位器\*1 | 红色 LED\*1 |
|  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4107849550a2c6d09592b2a28e291b54.png) |  |
| 220Ω电阻\*1 | USB 线\*1 | 跳线若干 |  |

**3.元件知识：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4a10a2fa7d595fb2683977c08657de34.png)

**可调电位器：** 可调电位器是电阻和模拟电子元件的一种，具有0和1两种状态(高电平和低电平)。模拟值不同，其数据状态呈现为1 ~ 1024等线性状态。

**ADC：** ADC是一种电子集成电路，用于将模拟信号(如电压)转换为由1和0组成的数字或二进制形式。我们在ESP32上的ADC的范围是12位，这意味着分辨率是2^12=4096，它表示一个范围(3.3V)将被平均划分为4096份，模拟值的范围对应于ADC值。因此，ADC拥有的比特越多，模拟的分区就越密集，最终转换的精度也就越高。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8f067a222c20bd1a949bb504f34df15d.png)

分节1:0V—(3.3/4095)V 范围内的模拟量对应数字0;

分节2:(3.3/4095)V—2\*(3.3/4095)V 范围内的模拟量对应于数字1;

......

下面的模拟将被相应地划分。换算公式如下：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/66f4a644cdb895d4a7910a3f1ac90ccd.png)

**DAC：** 这一过程的可逆需要DAC，数字到模拟转换器。数字I/O端口可以输出高电平和低电平(0或1)，但不能输出中间电压值，这就是DAC有用的地方。ESP32有两个8位精度的DAC输出引脚GPIO25和GPIO26，可以将VCC(这里是3.3V)分成2^8=256个部分。例如，当数字量为1时，输出电压值为3.3/256×1V，当数字量为128时，输出电压值为3.3/256 × 128=1.65V, DAC的精度越高，输出电压值的精度就越高。

换算公式如下：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0412cb6efce5c1d33e84aeac0931c402.png)

**ESP32主板上的ADC：**

ESP32有16个引脚，可以用来测量模拟信号。GPIO引脚序列号和模拟引脚定义如下表所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6e8bbcdf34c3bca663c73236fbb3f83f.png)

**ESP32主板上的DAC：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/379d9c501ea4dfc829b6b52dba5a13e7.png)

**4.读取电位器的ADC值，DAC值和电压值：**

我们将电位器连接到ESP32的模拟IO口上来读取电位器的ADC值，DAC值和电压值。接线请参照以下接线图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/669570530b1f544aea810426eff2c4c3.jpg)

```js
//**********************************************************************************
/*  
 * 文件名   : 读取电位器模拟值
 * 描述 : ADC,DAC和电压的基本用法
*/
#define PIN_ANALOG_IN  36  //电位器的引脚

void setup() {
  Serial.begin(115200);
}

//在loop()中，使用analogRead()函数获取ADC值，
//然后使用map()函数将值转换为8位精度的DAC值。
//输入输出电压下式计算，
//信息最终被打印出来.

void loop() {
  int adcVal = analogRead(PIN_ANALOG_IN);
  int dacVal = map(adcVal, 0, 4095, 0, 255);
  double voltage = adcVal / 4095.0 * 3.3;
  Serial.printf("ADC Val: %d, \t DAC Val: %d, \t Voltage: %.2fV\n", adcVal, dacVal, voltage);
  delay(200);
}
//**********************************************************************************
```
编译并上传代码到ESP32，代码上传成功后，利用USB线上电，打开串口监视器，设置波特率为115200。可以看到的现象是：串口监视器窗口将打印出电位器的ADC值，DAC值和电压值，转动电位器手柄时，ADC值，DAC值和电压值发生变化。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/aad8411c35d568962735bf062ce54ee2.png)

**5.调光灯的接线图：**

在前面一步，我们读取了电位器的ADC值，DAC值和电压值，现在我们需要将电位器的ADC值转换成LED的亮度，来做成一个亮度可调的灯。见如下所示接线图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/231f15fcdc1a50e5847ca582872499b6.jpg)

**6.项目代码：**

```js
//**********************************************************************************
/*  
 * 文件名  : 调光灯
 * 描述 : 通过电位器控制LED的亮度.
*/
#define PIN_ANALOG_IN    36  //电位器的引脚
#define PIN_LED     15  // LED的引脚
#define CHAN            0
void setup() {
  ledcSetup(CHAN, 1000, 12);
  ledcAttachPin(PIN_LED, CHAN);
}

void loop() {
  int adcVal = analogRead(PIN_ANALOG_IN); //读adc
  int pwmVal = adcVal;        // adcVal重映射到pwmVal
  ledcWrite(CHAN, pwmVal);    // 设置脉冲宽度.
  delay(10);
}
//**********************************************************************************
```

**7.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：转动电位器手柄，LED的亮度会相应地改变。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9bb5d9041ac5f0d5a4b20b31291457e6.png)

## 项目19 火焰警报器

**1.项目介绍：**

火灾是一种可怕的灾害，火灾报警系统在房屋，商业建筑和工厂中是非常有用的。在本项目中，我们将使用ESP32控制火焰传感器，蜂鸣器和LED来模拟火灾报警装置。这是一个有意义的创客活动。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 火焰传感器\*1 | 红色 LED\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a802ce7dc086858873e587c19b45578d.png) |  |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4107849550a2c6d09592b2a28e291b54.png) |
| 有源蜂鸣器\*1 | 220Ω电阻\*1 | 10KΩ电阻\*1 | 跳线若干 |
|  |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2266d29566a875c35c2bd4be3e4a23f8.png) |  |
| NPN型晶体管(S8050)\*1 | 1kΩ 电阻\*1 | USB 线\*1 |  |

**3.元件知识：**

**火焰传感器（红外接收三极管）：** 火焰会发出一定程度的IR光，这种光人眼是看不到的，但我们的红外接收三极管可以检测到它，并提醒微控制器（如ESP32）已经检测到火灾。红外接收三极管可以探测火焰，然后将火焰亮度转换为波动水平信号。红外接收三极管的短引脚是负极，另一个长引脚是正极。我们应该连接短引脚（负极)到5V，连接长引脚(正极)到模拟引脚，一个电阻和GND。如下图所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/dde086c54e0343400582892ae91d818a.png)

注意：火焰传感器应避开日光、汽车头灯、白炽灯直接照射，也不能对着热源(如暖气片、加热器)或空调，以避免环境温度较大的变化而造成误报。同时还易受射频辐射的干扰。

**4.读取火焰传感器的ADC值，DAC值和电压值：**

我们首先用一个简单的代码来读取火焰传感器的ADC值，DAC值和电压值并打印出来。接线请参照以下接线图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/5c151d185e7967e77ff4a8d3e17331f2.png)

```js
//**********************************************************************************
/*  
 * 文件名  : 读取火焰传感器的模拟值
 * 描述 : ADC,DAC和电压的基本用法
*/
#define PIN_ANALOG_IN  36  //火焰传感器的引脚

void setup() {
  Serial.begin(115200);
}

//在loop()中，使用analogRead()函数获取ADC值，
//然后使用map()函数将值转换为8位精度的DAC值。
//输入输出电压按下式计算，
//信息最终被打印出来.

void loop() {
  int adcVal = analogRead(PIN_ANALOG_IN);
  int dacVal = map(adcVal, 0, 4095, 0, 255);
  double voltage = adcVal / 4095.0 * 3.3;
  Serial.printf("ADC Val: %d, \t DAC Val: %d, \t Voltage: %.2fV\n", adcVal, dacVal, voltage);
  delay(200);
}
//**********************************************************************************
```

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，打开串口监视器，设置波特率为115200。可以看到的现象是：串口监视器窗口将打印火焰传感器读取的ADC值，DAC值和电压值，当火焰靠近火焰传感器时，ADC值，DAC值和电压值增大；反之，ADC值，DAC值和电压值减小。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/aad8411c35d568962735bf062ce54ee2.png)

**5.火焰报警的接线图：**

接下来，我们将使用火焰传感器和蜂鸣器、LED制作一个有趣的项目——火灾报警装置。当火焰传感器检测到火焰时，LED闪烁，蜂鸣器报警。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/056501565145dc46ce00dfb750d87e9f.png)

**6.项目代码：**

（注意：代码中的阀值500可以根据实际情况自己重新设置）

```js
//**********************************************************************************
/*  
 * 文件名 : 火焰警报
 * 描述 : 通过火焰传感器控制蜂鸣器和LED.
*/
#define PIN_ADC0      36  //火焰传感器的引脚
#define PIN_LED       15  // LED的引脚
#define PIN_BUZZER    4  // 蜂鸣器的引脚

void setup() {
  pinMode(PIN_LED, OUTPUT);
  pinMode(PIN_BUZZER, OUTPUT);
  pinMode(PIN_ADC0, INPUT);
}

void loop() {
  int adcVal = analogRead(PIN_ADC0); //读取火焰传感器的ADC值
  if (adcVal >= 500) {
    digitalWrite (PIN_BUZZER, HIGH); //蜂鸣器鸣叫
    digitalWrite(PIN_LED, HIGH); // 点亮 LED
    delay(500); // 延时0.5s.
    digitalWrite (PIN_BUZZER, LOW);
    digitalWrite(PIN_LED, LOW); // 熄灭 LED
    delay(500); // 延时0.5s.
  }
 else
 {
    digitalWrite(PIN_LED, LOW);  //熄灭 LED
    digitalWrite (PIN_BUZZER, LOW); //关闭蜂鸣器
  }
}
//**********************************************************************************
```

**7.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：当火焰传感器检测到火焰时，LED闪烁，蜂鸣器报警；否则，LED不亮，蜂鸣器不响。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1790b8a741eea4def3eac4e122049b01.png)

## 项目20 小夜灯

**1.项目介绍：**

传感器或元件在我们的日常生活中是无处不在的。例如，一些公共路灯在晚上会自动亮起，而在白天会自动熄灭。为什么呢? 事实上，这些都是利用了一种光敏元件，可以感应外部环境光强度的元件。晚上，当室外亮度降低时，路灯会自动打开；到了白天，路灯会自动关闭。这其中的原理是很简单的，在本实验中我们使用ESP32控制LED就来实现这个路灯的效果。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 光敏电阻\*1 | 红色 LED\*1 |
|  |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4107849550a2c6d09592b2a28e291b54.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2266d29566a875c35c2bd4be3e4a23f8.png) |
| 220Ω电阻\*1 | 10KΩ电阻\*1 | 跳线若干 | USB 线\*1 |

**3.元件知识：**

光敏电阻：是一种感光电阻，其原理是光敏电阻表面上接收亮度(光)降低电阻，光敏电阻的电阻值会随着被探测到的环境光的强弱而变化。有了这个特性，我们可以使用光敏电阻来检测光强。光敏电阻及其电子符号如下：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1905cf730948d3735f192c184361e2a2.png)

下面的电路是用来检测光敏电阻电阻值的变化：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7ad49eab2d60085716bb365d33f055bd.png)

在上述电路中，当光敏电阻的电阻因光强的变化而改变时，光敏电阻与电阻R2之间的电压也会发生变化。因此，通过测量这个电压就可以得到光的强度。本项目是采用上图左边的电路来接线的。

**4.读取光敏电阻的ADC值，DAC值和电压值：**

我们首先用一个简单的代码来读取光敏电阻的ADC值，DAC值和电压值并打印出来。接线请参照以下接线图：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c19e5b58e073c49a22dd0fdfdb38d7f7.png)

```js
//**********************************************************************************
/*  
 * 文件名  : 读取光敏传感器的模拟值
 * 描述 : ADC的基本用法
*/
#define PIN_ANALOG_IN  36  //光敏传感器的引脚

void setup() {
  Serial.begin(115200);
}

//在loop()中，使用analogRead()函数获取ADC值，
//然后使用map()函数将值转换为8位精度的DAC值。
//输入输出电压按下式计算，
//信息最终被打印出来.

void loop() {
  int adcVal = analogRead(PIN_ANALOG_IN);
  int dacVal = map(adcVal, 0, 4095, 0, 255);
  double voltage = adcVal / 4095.0 * 3.3;
  Serial.printf("ADC Val: %d, \t DAC Val: %d, \t Voltage: %.2fV\n", adcVal, dacVal, voltage);
  delay(200);
}
//**********************************************************************************
```
编译并上传代码到ESP32，代码上传成功后，利用USB线上电，打开串口监视器，设置波特率为115200。可以看到的现象是：串口监视器窗口将打印光敏电阻读取的ADC值，DAC值和电压值，当逐渐减弱光敏电阻所处环境中的光线强度时，ADC值，DAC值和电压值逐渐增大；反之，ADC值，DAC值和电压值逐渐减小. ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/aad8411c35d568962735bf062ce54ee2.png)

**5.光控灯的接线图：**

我们在前面做了一个小小的调光灯，现在我们来做一个光控灯。它们的原理是相同的，即通过ESP32获取传感器的模拟值，然后调节LED的亮度。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/eac0e08c0dde6b435907827d36eb1957.png)

**6.项目代码：**

```js
//**********************************************************************************
/*  
 * 文件名  : 夜光灯
 * 描述 : 通过光敏传感器控制LED亮度.
*/
#define PIN_ANALOG_IN    36  // 光敏传感器的引脚
#define PIN_LED     15  // LED的引脚
#define CHAN            0
#define LIGHT_MIN       372
#define LIGHT_MAX       2048
void setup() {
  ledcSetup(CHAN, 1000, 12);
  ledcAttachPin(PIN_LED, CHAN);
}

void loop() {
  int adcVal = analogRead(PIN_ANALOG_IN); //读取 adc
  int pwmVal = map(constrain(adcVal, LIGHT_MIN, LIGHT_MAX), LIGHT_MIN, LIGHT_MAX, 0, 4095);  // adcVal重映射到pwmVal
  ledcWrite(CHAN, pwmVal);    // 设置脉冲宽度.
  delay(10);
}
//**********************************************************************************
```

**7.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：当减弱光敏电阻所处环境中的光线强度时，LED变亮，反之，LED变暗。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b640e71ba0840d6f99fb089ab248ca4f.png)

## 项目21 温度仪表

**1.项目介绍：**

热敏电阻是一种电阻，其阻值取决于温度和温度的变化，广泛应用于园艺、家庭警报系统等装置中。因此，我们可以利用这一特性来制作温度计。

**2.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c4b08ad87310ea27bc06383257cc7f8f.png) |  |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | OLED屏\*1 | 热敏电阻\*1 |
|  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/25a2a8622647c1f1b576468786b0a762.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4107849550a2c6d09592b2a28e291b54.png) |
| 10KΩ电阻\*1 | 杜邦线若干 | USB 线\*1 | 跳线若干 |

**3.元件知识：**

**热敏电阻：** 热敏电阻是一种温度敏感电阻。当热敏电阻感应到温度的变化时，它的电阻就会发生变化。我们可以利用热敏电阻的这种特性来检测温度强度。热敏电阻及其电子符号如下所示。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4da14068ab4c20e5e807fdd08a82466f.png)

热敏电阻的电阻值与温度的关系为：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4efb34b50f2cdb5b2ed6f351ce475474.png)

式中：

Rt为热敏电阻在T2温度下的电阻；

R为热敏电阻在T1温度下的标称阻值；

EXP\[n\]是e的n次幂；

B为温度指数；

T1，T2是开尔文温度(绝对温度)，开尔文温度=273.15 +摄氏温度。对于热敏电阻的参数，我们使用：B=3950, R=10KΩ，T1=25℃。热敏电阻的电路连接方法与光敏电阻类似，如下所示：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/1ac82d185d19f35348be2eec7717d721.png)

我们可以利用ADC转换器测得的值来得到热敏电阻的电阻值，然后利用公式来得到温度值。因此，温度公式可以推导为：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/7a610a5fe8f27e2000432bcef2f765e9.png)

**4.读取热敏电阻的值：**

首先我们学习热敏电阻读取当前的ADC值、电压值和温度值并将其打印出来。请按下面的接线图接好线：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/aa12d903640a4b579c986272f0f7e163.png)

```js
//**********************************************************************************
/*  
 * 文件名 : 热敏电阻
 * 描述 : 读取热敏电阻的阻值.
*/
#define PIN_ANALOG_IN   36
void setup() {
  Serial.begin(115200);
}

void loop() {
  int adcValue = analogRead(PIN_ANALOG_IN);                       //读ADC引脚
  double voltage = (float)adcValue / 4095.0 * 3.3;                // 计算电压
  double Rt = 10 * voltage / (3.3 - voltage);                     //计算热敏电阻电阻值
  double tempK = 1 / (1 / (273.15 + 25) + log(Rt / 10) / 3950.0); //计算温度(开尔文)
  double tempC = tempK - 273.15;                                  //计算温度(摄氏)
  Serial.printf("ADC value : %d,\tVoltage : %.2fV, \tTemperature : %.2fC\n", adcValue, voltage, tempC);
  delay(1000);
}
//**********************************************************************************
```
编译并上传代码到ESP32，代码上传成功后，利用USB线上电，打开串口监视器，设置波特率为115200。你会看到的现象是：串口监视器窗口将不断显示热敏电阻当前的ADC值、电压值和温度值。试着用食指和拇指捏一下热敏电阻(不要碰触导线)一小段时间，你应该会看到温度值增加。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/590d50d10fd26eb05197edbf498ee7f4.png)

**5.温度仪表的接线图：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/5b77fc42f5fa8ab213447116da53f777.png)

**6.添加LCD\_128×32库：**

本项目代码使用了一个名为“LCD\_128×32”库。如果你已经添加好了“LCD\_128×32”库，则跳过此步骤。如果你还没有添加，请在学习之前安装它。添加第三方库的步骤如下:

打开Arduino IDE，单击“ **项目** ”→“ **包含库** ”→“ **添加.ZIP库...**”。在弹出窗口中找到该目录下名为**..\\库文件\\Arduino\_C\_Windows系统\\LCD\_128X32.ZIP** 的文件，先选中 **LCD\_128X32.ZIP** 文件，再单击“ **打开** ”。

![图片不存在](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0a8f5aa82bea27a65f8455e9a180de96.png)

**7.项目代码：**

```js
//**********************************************************************************
/*  
 * 文件名  : 温度仪表
 * 描述 : LCD显示热敏电阻的温度.
*/
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define PIN_ANALOG_IN   36
Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  Serial.begin(115200);

  // 初始化I2C，引脚为SDA=21, SCL=22
  Wire.begin(21, 22);

  // 初始化OLED显示屏
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { 
    Serial.println(F("SSD1306分配失败"));
  }

  Serial.println("OLED初始化成功");

  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE); // 设置文本颜色为白色
  display.setTextSize(1);  // 设置字体大小
  display.setCursor(0, 0); // 设置光标位置 (x, y)

  Serial.println("程序启动完毕");
}

void loop() {
  int adcValue = analogRead(PIN_ANALOG_IN);                       //读ADC引脚
  double voltage = (float)adcValue / 4095.0 * 3.3;                // 计算电压
  double Rt = 10 * voltage / (3.3 - voltage);                     //计算热敏电阻电阻值
  double tempK = 1 / (1 / (273.15 + 25) + (log(Rt / 10) / 3950.0)); //计算温度(开尔文)
  double tempC = tempK - 273.15;                                  //计算温度(摄氏)

  display.clearDisplay();
  display.setCursor(0,0);  display.print("Voltage:"); 
  display.setCursor(50,0); display.print(voltage);
  display.setCursor(75,0); display.print("V");

  display.setCursor(0,25); display.print("tempC:");
  display.setCursor(50,25); display.print(tempC);
  display.display();
  delay(1000);
}

//**********************************************************************************
```

**7.项目现象：**

编译并上传代码到ESP32，代码上传成功后，利用USB线上电，你会看到的现象是：LCD 128X32 DOT的屏幕上显示热敏电阻的电压值和当前环境中的温度值。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/073463d882655fe6ab03fdf8ed4c537b.png)

## 项目22 蓝牙

本章主要介绍如何通过ESP32的蓝牙与手机进行简单的数据传输。项目22.1 是常规蓝牙，项目22.2 是蓝牙控制LED。

**项目22.1 常规蓝牙**

**1\. 项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4d0be04892e2038c372f82caba521f64.png) |
| --- | --- | --- |
| ESP32\*1 | USB 线\*1 | 智能手机/平板电脑（自备）\*1 |

在本项目中，我们需要使用一个名为串口蓝牙终端的蓝牙应用程序来协助实验。如果您还没有安装它，请点击安装： [https://www.appsapk.com/serial-bluetooth-terminal/](https://www.appsapk.com/serial-bluetooth-terminal/) 。

下面是它的标志。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4d6bebf2b0859865e28b426bd3477fac.png)

**2.元件知识：**

蓝牙是一种短距离通信系统，可分为两种类型，即低功耗蓝牙(BLE)和经典蓝牙。简单的数据传输有两种模式：主模式和从模式。

**主模式：** 在这种模式下，工作在主设备上完成，并且可以与从设备连接。我们可以搜索和选择附近的从设备来连接。当设备在主模式下发起连接请求时，需要其他蓝牙设备的地址和配对密码等信息。配对完成后，可直接与它们连接。

**从模式：** 处于从模式的蓝牙模块只能接受来自主机的连接请求，但不能发起连接请求。与主机设备连接后，可以向主机设备发送数据，也可以从主机设备接收数据。蓝牙设备之间可以进行数据交互，一个设备处于主模式，另一个设备处于从模式。当它们进行数据交互时，处于主模式的蓝牙设备会搜索并选择附近要连接的设备。在建立连接时，它们可以交换数据。当手机与ESP32进行数据交换时，手机通常处于主模式，ESP32为从模式。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/086194049ec20b12b1a7cda437023331.png)

**3.项目接线：**

使用USB线将ESP32主板连接到电脑上的USB口。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/46cf3a8a1c79e456ac0f02da5ef38aec.png)

**4.项目代码：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a38430a2807c83e79c003cbe8054423c.png)

```js
//**********************************************************************************
/*
 * 文件名 : 经典的蓝牙
 * 描述 : ESP32通过蓝牙与手机通信，并通过串口打印手机数据
*/
#include "BluetoothSerial.h"

BluetoothSerial SerialBT;
String buffer;
void setup() {
  Serial.begin(115200);
  SerialBT.begin("ESP32test"); //蓝牙设备名称
  Serial.println("\nThe device started, now you can pair it with bluetooth!");
}

void loop() {
  if (Serial.available()) {
    SerialBT.write(Serial.read());
  }
  if (SerialBT.available()) {
    Serial.write(SerialBT.read());
  }
  delay(20);
}
//**********************************************************************************
```

**6.项目现象：**

编译并上传代码到ESP32，上传成功后，打开串行监视器，波特率设置为115200。当您看到串行打印出字符串，如下所示，表示ESP32的蓝牙已就绪，等待与手机连接。(如果打开串口监视器且设置波特率为115200之后，串口监视器窗口没有显示如下信息，可以按下ESP32的复位键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/515a31c10d414cae43980905e7df1879.jpg) ）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d0a0c882ace0c67de3ee19b193da139b.png)

请确认你的手机已开启手机蓝牙，且已安装“ **串口蓝牙终端** ”的蓝牙应用程序。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a934651544f086913a236e35568f7cd3.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4959397f4ab4f8f2e4c06ad6f29573a0.png)

手机自动搜索附近的蓝牙设备，点击“ESP32 test”进行配对，出现配对对话框，点击“ **配对** ”，这样“ESP32 test”设备就连接好了。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/d20253dd32917c9854ba20039af99f2d.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2c2aad793f4aa811e5bcc90cd303db85.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d2671b30598c93329ac52ad6d757f60.png)

打开软件APP，点击终端左侧。选择 "Devices"。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/48b660760c72d0e5ed654a8265875d16.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/72831d7e8b780025c88537b3236bfca3.png)

选择经典蓝牙模式下的ESP32test，会出现如下图所示的连接成功提示。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6e718c2e2c99c80c557b5c4941305a7d.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/023dba247c7da46105e2d68363ee443c.png)

现在，数据可以通过ESP32在你的手机和电脑之间传输。

在IDE中的文本框输入“Hi!”,当手机收到它的时候，给你的手机回复“Hi!”；手机发送“Hello!”，当电脑收到它的时候，给你的电脑回复“Hello!”。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3fcd5c21a9910f976d0b36d4bc625075.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/cbce154e5bbe1dc985183b3ed2125191.png)

**项目22.2 蓝牙控制LED**

**1.项目元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a2aa343488c11843f13ae0413547c673.png) |  | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/4d0be04892e2038c372f82caba521f64.png) |
| --- | --- | --- | --- |
| ESP32\*1 | 面包板\*1 | 红色LED\*1 | 智能手机/平板电脑（自备）\*1 |
| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a487df5effb3b0ae28e7601cad88c97b.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/8d920d12138bd3b4e62f02cecc2c63a3.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4421594adeb4676d63581a1047c6935.png) |  |
| 220Ω电阻\*1 | 跳线\*2 | USB 线\*1 |  |

**2.项目接线：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/6d79aa47daab6bc5dc46b1e62215d9c8.png)

**3.项目代码：**

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/b4a2130c4ed38886d593f17b83e8c9ee.png)

```js
//**********************************************************************************
/*
 * 文件名  : 蓝牙控制LED
 * 描述 : 手机通过蓝牙控制esp32指示灯.
          当手机发送“LED_on”时，ESP32的LED灯就会亮起.
          当手机发送“LED_off”时，ESP32的LED灯将关闭.
*/
#include "BluetoothSerial.h"
#include "string.h"
#define LED 15
BluetoothSerial SerialBT;
char buffer[20];
static int count = 0;
void setup() {
  pinMode(LED, OUTPUT);
  SerialBT.begin("ESP32test"); //蓝牙设备名称
  Serial.begin(115200);
  Serial.println("\nThe device started, now you can pair it with bluetooth!");
}

void loop() {
  while(SerialBT.available())
  {
    buffer[count] = SerialBT.read();
    count++;
  }
  if(count>0){
    Serial.print(buffer);
    if(strncmp(buffer,"led_on",6)==0){
      digitalWrite(LED,HIGH);
    }
    if(strncmp(buffer,"led_off",7)==0){
      digitalWrite(LED,LOW);
    }
    count=0;
    memset(buffer,0,20);
  }
}
//**********************************************************************************
```

**4.项目现象：**

编译并上传代码到ESP32。APP的操作与 **项目22.1** 相同，要使外接LED亮灭，只需将发送内容更改为"led\_on" 和 "led\_off" 即可。移动APP发送的数据：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/c0ecab67ea0f72fdb709ae5ee8c54dbe.png)

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/135a79adbe1c029257b69700b278d67c.png)

LED的现象：

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/53a0996330055250ffa3b190dede97bb.png)

注意： 如果发送的内容不是 "led\_on'或"led\_off"，那么LED的状态不会改变。如果LED是亮的，当接收到不相关内容时，LED继续亮；与之对应的是，如果LED是灭的，当接收到不相关内容时，LED继续熄灭。

## 项目23 WiFi 工作模式

在如今科技高速发展的时代，人们的生活质量越来越好，生活节奏越来越快，开始有人觉得复杂多样的智能化设备控制起来十分麻烦，通过手机统一控制智能化设备这种方法逐渐得到了人们的青睐。这种方法是利用单片机通过wifi模块和Internet网络建立手机和智能化设备之间的连接以此来实现对智能化设备的远程控制。在本章中，我们将重点关注ESP32的WiFi基础设施。ESP32有3种不同的WiFi工作模式：Station模式、AP模式和AP+Station模式。所有WiFi编程项目在使用WiFi前必须配置WiFi运行模式，否则无法使用WiFi。

**项目23.1: WIFI Station 模式**

**1.实验元件：**

| ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/2266d29566a875c35c2bd4be3e4a23f8.png) | ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/afc52f6616725ba37e3b12a2e01685ad.png) |
| --- | --- |
| USB 线 x1 | ESP32x1 |

**2.实验接线：**

使用USB线将ESP32主板连接到电脑上的USB口。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/3e0498944fc51e44b30bb90e2ae86b35.png)

**3.元件知识：**

**Station 模式：** 当ESP32选择Station模式时，它作为一个WiFi客户端。它可以连接路由器网络，通过WiFi连接与路由器上的其他设备通信。如下图所示，PC和路由器已经连接，ESP32如果要与PC通信，需要将PC和路由器连接起来。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/227039c89f94a5ffbc04bca9f9e61e40.png)

**4.项目代码：**

```js
//**********************************************************************************
/*
 * 文件名  : WiFi Station 模式
 * 模式 : 使用ESP32连接到路由器
*/
#include <WiFi.h> //包含ESP32的WiFi Library头文件.

//请输入正确的路由器名称和密码.
const char *ssid_Router     = "ChinaNet-2.4G-0DF0"; //输入路由器名称
const char *password_Router = "ChinaNet@233"; //输入路由器密码

void setup(){
  Serial.begin(115200);
  delay(2000);
  Serial.println("Setup start");
  WiFi.begin(ssid_Router, password_Router);//将ESP32设置为“Station 模式”，并连接到路由器。
  Serial.println(String("Connecting to ")+ssid_Router);
//每0.5s检查ESP32是否连接路由器成功.  
  while (WiFi.status() != WL_CONNECTED){
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnected, IP address: ");
  Serial.println(WiFi.localIP());//串行监视器打印出分配给ESP32的IP地址.
  Serial.println("Setup End");
}

void loop() {
}
//**********************************************************************************
```
由于各地的WiFi名称和密码是不同，所以在程序代码运行之前，用户需要在下图所示的框中输入正确的WiFi名称和密码。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/860aa375650b2334878e22a3d338bc9b.png)

**5.项目现象：**

确认正确输入WiFi名称和密码后，编译并上传代码到ESP32主板，上传成功后，打开串口监视器，设置波特率为115200。当ESP32成功连接到ssid\_Router时，串行监视器将打印出WiFi分配给ESP32的IP地址。然后串口监视器窗口将显示如下：(如果打开串口监视器且设置波特率为115200之后，串口监视器窗口没有显示如下信息，可以按下ESP32的复位键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/515a31c10d414cae43980905e7df1879.jpg) ）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/538e56d1f4cded360c56f6db9852f7f3.png)

**项目23.2：WIFI AP 模式**

**1.实验元件&接线：**

实验元件和实验接线与上面的 **项目23.1: WIFI Station 模式** 相同。

**2.元件知识：**

**AP模式：** 当ESP32选择AP模式时，会创建一个独立于Internet的热点网络，等待其他WiFi设备连接。如下图所示，以ESP32为热点。如果手机或PC需要与ESP32通信，则必须连接到ESP32的热点。只有通过ESP32建立连接后才能进行通信。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/9117763df4e25fdca6bc7e576bc91ded.png)

**3.项目代码：**

```js
//**********************************************************************************
/*
 * 文件名  : WiFi AP
 * 描述 : 设置ESP32打开接入点
*/
#include <WiFi.h> //包含ESP32的WiFi Library头文件.

const char *ssid_AP     = "ESP32_Wifi"; //输入AP名称
const char *password_AP = "12345678"; //输入AP密码

IPAddress local_IP(192,168,1,108);//设置ESP32本身的IP地址
IPAddress gateway(192,168,1,1);   //设置ESP32本身的网关
IPAddress subnet(255,255,255,0);  //设置ESP32本身的子网掩码

void setup(){
  Serial.begin(115200);
  delay(2000);
  Serial.println("Setting soft-AP configuration ... ");
  WiFi.disconnect();
  WiFi.mode(WIFI_AP);
  Serial.println(WiFi.softAPConfig(local_IP, gateway, subnet) ? "Ready" : "Failed!");
  Serial.println("Setting soft-AP ... ");
  boolean result = WiFi.softAP(ssid_AP, password_AP);
  if(result){
    Serial.println("Ready");
    Serial.println(String("Soft-AP IP address = ") + WiFi.softAPIP().toString());
    Serial.println(String("MAC address = ") + WiFi.softAPmacAddress().c_str());
  }else{
    Serial.println("Failed!");
  }
  Serial.println("Setup End");
}

void loop() {
}
//**********************************************************************************
```
在程序代码运行之前，你可以像下图所示对框中ESP32的AP名称和密码进行任何更改。当然，在默认情况下，你也可以不修改它。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/be9accc8fe39ff50f3a35be6f9800b92.png)

**4.项目现象：**

正确输入ESP32的AP名称和密码，编译并上传代码到ESP32主板，上传成功后，打开串口监视器，设置波特率为115200。然后串口监视器窗口将显示如下：(如果打开串口监视器且设置波特率为115200之后，串口监视器窗口没有显示如下信息，可以按下ESP32的复位键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/515a31c10d414cae43980905e7df1879.jpg) ）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/fada6409bd5fde4ace40c74e3404d414.png)

观察串口监视器打印信息时，打开手机WiFi扫描功能，可以看到ESP32上的ssid\_AP被调用，在本程序代码中称为“ESP32\_Wifi”。你可以输入密码“12345678”连接它，也可以通过修改程序代码来修改它的AP名称和密码。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/29b419a83fcc3ddcfa32f08296d8e9f7.png)

**项目23.3：WIFI AP+Station模式**

**1.实验元件&接线：**

实验元件和实验接线与上面的 **项目23.1: WIFI Station 模式** 和 **项目23.2: WIFI AP 模式** 相同。

**2.元件知识：**

**AP+Station模式：** ESP32除AP模式和Station模式外，还可以同时使用AP模式和Station模式。此模式包含前两种模式的功能。打开ESP32的Station模式，将其连接到路由器网络，它可以通过路由器与Internet通信。同时开启其AP模式，创建热点网络。其他WiFi设备可以选择连接路由器网络或热点网络与ESP32通信。

**3.项目代码：**

```js
//**********************************************************************************
/*
 * 文件名 : WiFi AP+Station
 * 描述 : ESP32连接到用户的路由器，打开一个接入点
*/
#include <WiFi.h>

const char *ssid_Router     =  "ChinaNet-2.4G-0DF0";  //输入路由器名称
const char *password_Router =  "ChinaNet@233";  //输入路由器密码
const char *ssid_AP         =  "ESP32_Wifi"; //输入AP名称
const char *password_AP     =  "12345678";  //输入AP密码

void setup(){
  Serial.begin(115200);
  Serial.println("Setting soft-AP configuration ... ");
  WiFi.disconnect();
  WiFi.mode(WIFI_AP);
  Serial.println("Setting soft-AP ... ");
  boolean result = WiFi.softAP(ssid_AP, password_AP);
  if(result){
    Serial.println("Ready");
    Serial.println(String("Soft-AP IP address = ") + WiFi.softAPIP().toString());
    Serial.println(String("MAC address = ") + WiFi.softAPmacAddress().c_str());
  }else{
    Serial.println("Failed!");
  }

  Serial.println("\nSetting Station configuration ... ");
  WiFi.begin(ssid_Router, password_Router);
  Serial.println(String("Connecting to ")+ ssid_Router);
  while (WiFi.status() != WL_CONNECTED){
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConnected, IP address: ");
  Serial.println(WiFi.localIP());
  Serial.println("Setup End");
}

void loop() {
}
//**********************************************************************************
```
它类似于上面的 **项目23.1** 和 **项目23.2** 。在运行代码之前，你需要修改ssid\_Router、password\_Router、ssid\_AP和password\_AP，如下图中的红色框所示。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/a581ba0f8f09a799f76a6369ed768fc0.png)

**4.项目现象：**

确认程序代码修改正确后，编译并上传代码到 ESP32主板上，上传成功后，打开串口监视器，设置波特率为115200。然后串口监视器窗口将显示如下：(如果打开串口监视器且设置波特率为115200之后，串口监视器窗口没有显示如下信息，可以按下ESP32的复位键 ![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/515a31c10d414cae43980905e7df1879.jpg) ）

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/0a0b4e200d48018ee19edcde6b27ec9c.png)

打开手机的WiFi搜索功能，可以在ESP32上看到ssid\_AP被调用。

![](https://doc.7gp.cn/%E5%88%9D%E7%BA%A7%E7%89%88/Arduino/media/29b419a83fcc3ddcfa32f08296d8e9f7.png)