# Spectrum 链开发--IDE 断点调试介绍

`编辑人：@GirpZhang 审阅人：@rectinajh`

## 开发环境

* Windows10

* go1.9.2.windows-amd64

* Visual Studio Code

* Git 2.18.0

## 需要完成 Visual Studio Code + GoLang 开发环境配置

* 安装 go1.9.2.windows-amd64，配置 GOPATH

* 安装 Git

    `本例中使用的 "GOPATH":"E:/GoPath";"GOROOT":"E:/Go"`

* 安装 Visual Studio Code，安装 Go 插件，介绍中是 Rich Go language support for Visual Studio Code

* 调试配置：

    `VSCode->查看->调试->添加调试目标,在"没有调试"的下拉框中点击"添加配置.."` 添加目标调试配置:

```

{

    "version": "0.2.0",

    "configurations": [

        {

            "name": "Launch",

            "type": "go",

            "request": "launch",

            "mode": "debug",

            "remotePath": "",

            "port": 2345,

            "host": "127.0.0.1",

            "program": "${fileDirname}",

            "env": {

                "GOPATH":"E:/GoPath",

                "GOROOT":"E:/Go"

            },

            "args": [],

            "showLog": true

        }

    ]

}

```

## 下载 Spectrum 代码

```

git clone git@github.com:SmartMeshFoundation/Spectrum.git

```

## 调试准备

* 在 E:/GoPath/src 下新建多级目录：\github.com\SmartMeshFoundation\Spectrum

* 将下载下来的 Spectrum.git 中的代码复制到 E:/GoPath/src/github.com/SmartMeshFoundation/Spectrum 下
![](https://upload-images.jianshu.io/upload_images/7286323-a61dfeaaab647cc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 打开 VSCode，选择 文件->打开文件夹->打开 Spectrum 文件夹

* 打开 cmd->smc->main.go 文件，如下：
![](https://upload-images.jianshu.io/upload_images/7286323-46fe6f7bc7856763.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* F5 调试，会提示如下错误：

```

Failded to continue:"Cannot find Delve debugger. Install from https://github.com/derekparker/delve & ensure it is in your "GOPATH/bin" or "PATH"

```

* 然后我们使用 Go 命令行编译调试器、将 dlv 调试器放在 GOPATH 的 bin 目录下

    * 使用 cmd 命令行，进入目录 E:\GoPath\bin

    * 执行命令安装 dlv

    ```

    go get github.com/derekparker/delve/cmd/dlv

    ```
![](https://upload-images.jianshu.io/upload_images/7286323-f159fbf2072d15bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 安装完成后就可以正常调试了，在需要的地方打上断点进行单步调试：

![](http://upload-images.jianshu.io/upload_images/7286323-4518d81e757eabee?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
