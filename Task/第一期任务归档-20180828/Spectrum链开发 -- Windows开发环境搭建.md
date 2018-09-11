
编辑人：@牛志成 审阅人：@rectinajh


1 环境准备

1.1 操作系统环境

Windows 7 专业版

1.2 我们首先安装GO的工具包

1.2.1 下载 并且 安装Go安装包（需要V1.7+以上版本）Goland的V1.10.3官网最新下载地址：https://dl.google.com/go/go1.10.3.windows-amd64.msi需要梯子】，最新的百度下载地址如下所示:

Windwos 32位系统 百度下载链接：https://pan.baidu.com/s/1_uR-rEiOwDYPYcHwLCBZwA密码：qdyg

Windows 64位系统 百度下载链接：https://pan.baidu.com/s/1mKeVZ9mWVcWUE0JJeaQ40w密码：s91q

选择自己windows系统合适的版本， 这里我选择go1.10.3.windows-amd64.msi进行安装，下载下来后，双击点击文件进行安装，大家可以自己自定义安装路径，这里我安装的路径为E:\Go。

1.2.2 配置Go环境变量

一 如果是msi安装文件，Go语言的环境变量会自动设置好。

二 配值环境变量的配置， 具体参考如下：

(1). 变量名：GOPATH 变量值 ：E:\Gopath   注：Gopath 这个文件夹是我在E盘下面手动建立作为资源存放

1.2.3.下面是环境变量设置的地方 这里为大家截图展示


![image1](https://upload-images.jianshu.io/upload_images/13976285-115945283a00206c.png)
![image1](https://upload-images.jianshu.io/upload_images/13976285-2f465bca9839b5a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/830/format/webp)
![image1](https://upload-images.jianshu.io/upload_images/13976285-acf25d9b3534feaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/472/format/webp)

1.2.4 验证Go安装环境是否成功

打开Windows中的命令提示符（cmd.exe）

执行命令：go version 正常情况下会显示：go version go1.10.3 windows/amd64如果出现 “’go‘不是内部或外部命令，也不是可运行的程序或批处理文件”， 请参考“1.2.2 配置Go环境变量”重新设置一下，直到验证成功为止。

这里显示出版本号即为成功 安装GO

![image1](https://upload-images.jianshu.io/upload_images/13976285-032939243ad6a88e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/673/format/webp)

1.3 安装Git

选择最新Git下载：

64位git-2.18.0-64-bit.exe下载地址:https://github.com/git-for-windows/git/releases/download/v2.18.0.windows.1/Git-2.18.0-64-bit.exe

32位git-2.1.8.0-32-bit.exe下载地址：https://github.com/git-for-windows/git/releases/download/v2.18.0.windows.1/Git-2.18.0-64-bit.exe

下载完成后，双击安装，这里我选在64位git-2.18.0-64-bit.exe， 安装路径用户可以自定义，我的安装路径为E:\Program Files\Git，在该路径下面找到git-bash.exe文件，双击打开，会弹出一个MinGW64的命令行窗口，就可以执行git相关命令了， 
执行git --version ,如果显示版本即成功。

![image1](https://upload-images.jianshu.io/upload_images/13976285-fea988fccfa5fc9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/569/format/webp)


1.4 安装编译器GCC

1.由于要在windows上编译Spectrum代码，我们需要安装一个编译器MinGW， 
MinGW全称Minimalist GNU For Windows,是个精简的Windows平台C/C++、ADA及Fortran编译器。 
MinGW-w64-for32 and 64 bit Window 
下载地址：https://sourceforge.net/projects/mingw-w64/files/latest/download，
下载完成后，就开始安装了，64位电脑安装配置如下所示：

![image1](https://upload-images.jianshu.io/upload_images/13976285-7417c8b922a6ee1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/523/format/webp)

2.配置gcc的环境变量 

这里我的安装目录是：E:\GCC

环境变量我们再 PATH里面 追加 E:\GCC\mingw64\bin   自己安装Gcc的bin文件夹

环境变量的设置方法参考：1.2.3

![image1](https://upload-images.jianshu.io/upload_images/13976285-439f12efc14812ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/987/format/webp)

1.5 设置Spectrum资源存放的文件夹

1.5.1 首先GOPATH设置的文件夹里面一次建立文件夹

我的GOPATH 对应的文件夹是 E:\Gopath

src ——》github.com----》SmartMeshFoundation  依次建立文件夹

最终我们建立的目标路径：E:\Gopath\src\github.com\SmartMeshFoundation

1.5.2 编译Spectrum

打开cmd 切换到我们1.5.1设置的文件夹路径

![image1](https://upload-images.jianshu.io/upload_images/13976285-cd998da9b1c1d6f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/560/format/webp)

1.5.4 把Spectrum的源码文件下载下来

执行命令   git clone https://github.com/SmartMeshFoundation/Spectrum

![image1](https://upload-images.jianshu.io/upload_images/13976285-5fa88b6e837e83b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/653/format/webp)

1.5.5  进度达到100%以后我们可以到我们设置的文件夹 查看

E:\Gopath\src\github.com\SmartMeshFoundation

![image1](https://upload-images.jianshu.io/upload_images/13976285-df408736d37c86dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/956/format/webp)


1.5.6 切换到Spectrum文件夹  

执行编译命令

go install -v ./cmd/smc

![image1](https://upload-images.jianshu.io/upload_images/13976285-3175db58ad1c4781.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/649/format/webp)
![image1](https://upload-images.jianshu.io/upload_images/13976285-6e75ed740ab6a8f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/797/format/webp)

1.5.7 测试编译是否成功

![image1](https://upload-images.jianshu.io/upload_images/13976285-cb2eea52398941c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/635/format/webp)
![image1](https://upload-images.jianshu.io/upload_images/13976285-fe3776b5a40d59fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/670/format/webp)

出现开始同步节点，即为编译成功

问题总结：

初步编译Spectrum出现问题 编译失败，出现找不到部分文件

解决方法：

重新删除GO语言安装环境，重新安装

![image1](https://upload-images.jianshu.io/upload_images/10170711-955610c93d589d92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/507/format/webp)


