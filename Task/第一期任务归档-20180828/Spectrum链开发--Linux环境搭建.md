# Spectrum链开发--Linux环境搭建

编辑人：@ruur
审阅人：@朱春雷

本文主要介绍在Linux环境下Spectrum链开发的基础环境安装，包括Ubuntu和CentOS环境。同时，也提供了打造Docker镜像的方法。推荐使用docker环境安装，能够避免因环境的差异造成安装不成功，也可快速部署。

## 安装

### 系统环境(以下环境已测试通过)

* Ubuntu: Ubuntu 16.04.4 LTS

* CentOS: CentOS Linux release 7.5.1804

* Docker: Version 18.06.1-ce-mac73 (26764)

 > 注：不同Linux发布版本安装过程可能略有不同

### 工具安装

#### 安装依赖

* Ubuntu

```
$ apt-get install git
$ apt-get install wget
```

* CentOS

```
$ yum install git
$ yum install wget
$ yum -y install gcc automake autoconf libtool make
```

* Docker(使用Golang镜像，无需手动安装Go)

```
$ docker pull golang
$ docker run -i -t golang /bin/bash
$ go version
go version go1.11 linux/amd64
```

### 安装Go
#### 版本检查
```
$ go version
```
#### 版本支持
* 经测试，Golang1.9.2 -- Golang1.11均可
* 安装项目之前需检查是否安装了符合要求的Golang，若已安装或使用Golang的Docker镜像，请忽略下一个步骤【下载及安装】

#### 下载及安装

##### 下载及解压

```
$ wget -c -t 3 https://dl.google.com/go/go1.9.2.linux-amd64.tar.gz
$ tar -C /usr/local -xzf go1.9.2.linux-amd64.tar.gz
```

##### 设置环境变量
* 执行
```
$ export GOROOT=/usr/local/go
$ export PATH=$GOROOT/bin:$PATH
```
* 也可把环境变量添加到profile文件中，把以上环境变量加入到 `$HOME/.profile` 文件中，同时执行 `source $HOME/.profile`

#### 检查版本

* 执行
```
$ go version
```

* 结果
```
go version go1.9.2 linux/amd64
```

* Golang安装过程如下图：![install.png](https://upload-images.jianshu.io/upload_images/1519044-bb3bd728b43da06a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Spectrum安装
#### 下载源文件
```
$ git clone https://github.com/SmartMeshFoundation/Spectrum.git
```

#### 编译
```
$ cd Spectrum
$ make smc
```

#### 编译结果
* 编译结果如下表示即成功
```
Done building.
Run "/your_path/Spectrum/build/bin/smc" to launch smc.
```
* 编译过程如下图：![build.png](https://upload-images.jianshu.io/upload_images/1519044-02c57c022a47e968.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 运行
#### 执行命令
```
$ /your_path/Spectrum/build/bin/smc console
```
> 注： `/your_path/`表示Spectrum项目代码所在的系统路径

* 运行过程如下图：![run.png](https://upload-images.jianshu.io/upload_images/1519044-fc969eecc5dde606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 创建账号

```
> personal.newAccount()
```

* 创建账号如下图：![new.png](https://upload-images.jianshu.io/upload_images/1519044-74b2dc3a3c676f85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 查看节点

```
> tribe.getStatus()
```

* 查看节点如下图：![status.png](https://upload-images.jianshu.io/upload_images/1519044-e3ee0e628f231091.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 查看历史

```
> tribe.getHistory(11,false)
```

* 查看历史如下图：![history.png](https://upload-images.jianshu.io/upload_images/1519044-81dc693d8d8282ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 其他
* 完成以上安装过程即表示Spectrum链基础环境已安装成功，可以进行接下来的开发工作。
* 执行`/your_path/Spectrum/build/bin/smc console`命令时可能会同步下载大量的Spectrum链数据，这可能会需要数十分钟至几个小时（依网络情况而定）。
* 另外，请保证系统环境有足够的空间可完成数据的下载更新。数据存储路径为 `~/.spectrum/smc/chaindata/`，如需移除项目，请同步删除数据文件，操作命令为： `rm -rf ~/.spectrum`。
* 中文版发布：https://www.jianshu.com/p/92d067c44b3c
* 英文版发布：https://medium.com/@ruur/spectrum-chain-installation-for-linux-ae0a9e59179
