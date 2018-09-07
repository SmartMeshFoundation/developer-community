How to build Spectrum under Mac OS
==================================

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

