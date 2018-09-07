How to build Spectrum under Mac OS
==================================

editor：@sxsong04 reviewer：@rectinajh

## environment setup
#### install golang
  ```
  brew install go
  ```
#### setup golang environment variables
  ```
  vim ~/.bash_profile
  ```
   edit ~/.bash_profile , add $GOPATH
  ```
  #replace ~/go_project with your path
  export GOPATH=~/go_project   
  export PATH=.:$PATH:$GOPATH:$GOPATH/bin
  ```
create src，pkg，bin folder under $GOPATH
  ```
mkdir -p $GOPATH/bin $GOPATH/pkg $GOPATH/src
  ```
test go
  ```
  source ~/.bash_profile
  go env
  ```
#### install git
  ```
  brew install git
  ```
---
## build from source
clone latest source code to $GOPATH
  ```
mkdir -p $GOPATH/src/github.com/SmartMeshFoundation
cd $GOPATH/src/github.com/SmartMeshFoundation
git clone https://github.com/SmartMeshFoundation/Spectrum
  ```
build smc
  ```
  cd $GOPATH/src/github.com/SmartMeshFoundation/Spectrum/
  go install -v ./cmd/smc
  ```
smc excutable file：$GOPAT/bin/smc
test smc
  ```
  smc console
  ```
---
## About IDE
The popular golang IDE under Mac are GoLand，VSCode，GoVim...，choose the IDE you like.

Take GoLand as an example to debug Spectrum.

File -> Open -> $GOPATH/src/github.com/SmartMeshFoundation/Spectrum/

go to cmd/smc/main.go, Press F5.

![Screen Shot 2018-09-06 at 3.48.14 PM.png](https://upload-images.jianshu.io/upload_images/13870844-025f9bb19996a07d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
