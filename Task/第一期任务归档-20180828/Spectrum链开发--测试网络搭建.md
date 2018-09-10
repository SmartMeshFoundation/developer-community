本文将演示在服务器端搭建Spectrum测试网络的过程，操作系统为CentOS 7.4 64位，已安装并配置好go运行环境，同时已下载Spectrum源码并成功编译。对上述系统环境搭建不熟悉的读者，可以参考《[Spectrum链开发--Linux环境搭建](https://github.com/SmartMeshFoundation/developer-community/blob/master/Task/%E7%AC%AC%E4%B8%80%E6%9C%9F%E4%BB%BB%E5%8A%A1%E5%BD%92%E6%A1%A3-20180828/Spectrum%E9%93%BE%E5%BC%80%E5%8F%91--Linux%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA.md)》一文。

#### 1. 初始化创世区块
##### 1.1 准备创世区块配置文件genesis.json
首先，新建一个目录用来存放genesis.json。假设新建目录为\~/spectrumchain，genesis.json保存在\~/spectrumchain中，目录结构如下：
```
spectrumchain
└── genesis.json			
```
然后，编辑genesis.json文件，内容如下：
```
{
  "config": {
        "chainId": 20180908,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
  },
  "coinbase"   : "0x0000000000000000000000000000000000000000",
  "difficulty" : "0x20000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000042",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00",
  "alloc"      : {}
}
```
主要参数介绍：
参数名称 | 说明
---|---
chainId | 区块链ID，下文中出现的 --networkid 参数需要与 chainId 值一致
coinbase | 矿工账号
difficulty | 难度值
extraData | 附加信息
gasLimit | gas的消耗总量限制，用来限制区块能包含的交易信息总和
nonce | 64位随机数，用于挖矿
mixhash | 与nonce配合用于挖矿
parentHash | 上一个区块的hash值，因为是创世块，所以这个值是0
timestamp | 设置创世块的时间戳
alloc | 预置账号以及账号的SMT数量

##### 1.2 写入创世区块	
准备好创世区块配置文件后，需要初始化Spectrum链，将上述创世区块信息写入Spectrum中。在spectrumchain目录中新建一个目录spectrumdata，用来存放区块链数据，此时目录结构如下：			
```
spectrumchain
├── spectrumdata
└── genesis.json			
```
进入spectrumchain目录中，执行初始化命令：
```
$ cd spectrumchain
$ smc --datadir spectrumdata init genesis.json
```
上面的命令主体是smc init，表示初始化Spectrum链，命令可以带有选项和参数，其中--datadir选项后面跟目录名，这里为spectrumdata，表示指定区块链数据存放目录为spectrumdata，genesis.json是init命令的参数，即创世区块配置文件。

执行命令后，会读取genesis.json文件，根据其内容将创世区块写入到区块链中。如果看到以下的输出内容，说明初始化成功了。
```
Successfully wrote genesis state database=lightchaindata hash=5e1fc7…d790e0
```
初始化成功后，会在数据目录spectrumdata中生成smc和keystore两个文件夹，此时目录结构如下：
```
spectrumchain
├── spectrumdata
│   ├── smc
│   │   └── chaindata
│   │       ├── 000001.log
│   │       ├── CURRENT
│   │       ├── LOCK
│   │       ├── LOG
│   │       └── MANIFEST-000000
│   │   └── lightchaindata
│   │       ├── 000001.log
│   │       ├── CURRENT
│   │       ├── LOCK
│   │       ├── LOG
│   │       └── MANIFEST-000000
│   └── keystore
└── genesis.json
```
其中smc/chaindata中存放的是区块数据，keystore中存放的是账户数据。

#### 2. 启动测试网络
初始化完成后，可以启动测试网络了。执行下面的命令启动节点：
```
$ smc --datadir spectrumdata --networkid 20180908 console
```
上面命令的主体是smc console，表示启动节点并进入交互式控制台，--datadir选项指定使用spectrumdata作为数据目录，--networkid选项后必须与genesis.json中的chainId一致，在连接到其他节点时会用到。

执行上述命令后，就启动了区块链节点并进入控制台：
```
...
Welcome to the Geth JavaScript console!

instance: Smc/v0.5.1-unstable-9bbe523c/linux-amd64/go1.9.4
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> 
```

#### 3. 使用测试网络
##### 3.1 创建账户
启动了测试网络后，就可以创建账户了。初次运行的测试网络是没有任何账户的，可以在console中输入eth.accounts查看：
```
> eth.accounts
[]
```
可以使用personal.newAccount()创建一个账户，会提示输入密码和确认密码，输入密码不会有显示，回车后返回新创建的账户地址：
```
> personal.newAccount()
Passphrase: 
Repeat passphrase: 
"0xc4cc8cd3c8a4e6a05cf21d182ebbf41868401275"
```
可以创建多个账户，为方便演示账户间的交易，再来创建一个账户：
```
> personal.newAccount()
Passphrase: 
Repeat passphrase: 
"0x9358844d589f31c5c5515d65d156c3233d2fdc24"
```
再输入eth.accounts就可以查看到两个账户了：
```
> eth.accounts
["0xc4cc8cd3c8a4e6a05cf21d182ebbf41868401275", "0x9358844d589f31c5c5515d65d156c3233d2fdc24"]
```
账户默认会保存在数据目录的keystore文件夹中。查看目录结构，发现spectrumdata/keystore中多了两个文件，这两个文件对应新创建的两个账户。这是json格式的文本文件，内容是私钥经过加密后的信息。
```
spectrumdata
├── smc
├── smc.ipc
├── history
└── keystore
    ├── UTC--2018-09-08T09-37-02.526965006Z--c4cc8cd3c8a4e6a05cf21d182ebbf41868401275
    └── UTC--2018-09-08T09-38-02.713900919Z--9358844d589f31c5c5515d65d156c3233d2fdc24
```

##### 3.2 查询账户余额
eth.getBalance()可以查看指定账户的余额：
```
> eth.getBalance(eth.accounts[0])
0
> eth.getBalance(eth.accounts[1])
0
```
当前两个账户的余额都是0，要使账户有余额，可以从其他账户转账过来，或者通过挖矿来获得奖励。

##### 3.3 启动&停止挖矿
miner.start()可以启动挖矿：
```
> miner.start(1)
```
其中start的参数表示挖矿使用的线程数。第一次启动挖矿会生成挖矿所需的DAG文件，在生成进度达到100%后，就会开始挖矿，此时屏幕会被挖矿信息刷屏。

miner.stop()可以停止挖矿：
```
> miner.stop()
```
注意：输入的字符可能会被挖矿刷屏信息冲掉，不用担心，只要输入完整的miner.stop()回车，即可停止挖矿。

挖矿所得的奖励会进入矿工的账户，这个账户叫做coinbase，默认情况下coinbase是本地账户中的第一个账户：
```
> eth.coinbase
"0xc4cc8cd3c8a4e6a05cf21d182ebbf41868401275"
```
挖到区块以后，coinbase中就有余额了：
```
> eth.getBalance(eth.coinbase)
150000000000000000000
```
getBalance()返回值的单位是wei，1个SMT=10的18次方个wei。要查看有多少个SMT，可以用web3.fromWei()将返回值换算成SMT：
```
> web3.fromWei(eth.getBalance(eth.accounts[0]),'ether')
150
```
如果想使用别的账户作为coinbase，可以通过miner.setEtherbase()将其他账户设置成coinbase：
```
> miner.setEtherbase(eth.accounts[1])
true
> eth.coinbase
"0x9358844d589f31c5c5515d65d156c3233d2fdc24"
```

##### 3.4 发送交易
目前，两个账户余额分别是：
```
> eth.getBalance(eth.accounts[0])
150000000000000000000
> eth.getBalance(eth.accounts[1])
0
```
可以发送一笔交易，从eth.accounts[0]向eth.accounts[1]转移5个SMT：
```
> amount = web3.toWei(5,'ether')
"5000000000000000000"
> eth.sendTransaction({from:eth.accounts[0], to:eth.accounts[1], value:amount})
Error: authentication needed: password or unlock
    at web3.js:3143:20
    at web3.js:6347:15
    at web3.js:5081:36
    at <anonymous>:1:1
```
这里报错了，因为要发送交易，必须先解锁账户：
```
> personal.unlockAccount(eth.accounts[0])
Unlock account 0xc4cc8cd3c8a4e6a05cf21d182ebbf41868401275
Passphrase: 
true
```
输入账户密码就可以成功解锁账户。然后再发送交易：
```
> amount = web3.toWei(5,'ether')
"5000000000000000000"
> eth.sendTransaction({from:eth.accounts[0], to:eth.accounts[1], value:amount})
INFO [09-08|20:08:40] Submitted transaction                    fullhash=0x250470a72b41109aab1b64cb487b8f5c5754a03203b9a4243eb97a1a6d8f2521 recipient=0x9358844d589f31C5c5515D65d156c3233D2fDC24
"0x250470a72b41109aab1b64cb487b8f5c5754a03203b9a4243eb97a1a6d8f2521"
```
此时交易已经提交到区块链，返回了交易的hash，但还未被处理，可以使用txpool.status来验证：
```
> txpool.status
{
  pending: 1,
  queued: 0
}
```
其中有一条pending的交易，pending表示已提交但还未被处理的交易。

要使交易被处理，必须要挖矿。这里我们启动挖矿，然后等待挖到一个区块之后就停止挖矿：
```
> miner.start(1); admin.sleepBlocks(1); miner.stop();
true
```
当miner.stop()返回true后，再执行txpool.status会发现pending的数量已变为0，说明交易已经被处理了：
```
> txpool.status
{
  pending: 0,
  queued: 0
}
```
此时，交易已经生效，eth.accounts[1]中应该已收到了5个SMT了：
```
> web3.fromWei(eth.getBalance(eth.accounts[1]),'ether')
5
```
通过eth.getTransaction()可以查看此笔交易信息：
```
> eth.getTransaction("0x250470a72b41109aab1b64cb487b8f5c5754a03203b9a4243eb97a1a6d8f2521")
{
  blockHash: "0x1ce828d9dce08dc7868f3aab92f22d7115186515cdbc426d9a7b8a6d96ae979d",
  blockNumber: 35,
  from: "0xc4cc8cd3c8a4e6a05cf21d182ebbf41868401275",
  gas: 90000,
  gasPrice: 18000000000,
  hash: "0x250470a72b41109aab1b64cb487b8f5c5754a03203b9a4243eb97a1a6d8f2521",
  input: "0x",
  nonce: 0,
  r: "0x1577c4f7a78e6b37e5159b2488d5636feb2bbb5ef10e8b01484155dc3710e884",
  s: "0x2837e1477e77431a7ab946de5d96ced06b71bab364453e1d36ffba7caf65acc3",
  to: "0x9358844d589f31c5c5515d65d156c3233d2fdc24",
  transactionIndex: 0,
  v: "0x267df7b",
  value: 5000000000000000000
}
```

#### 4. 连接到其他节点
节点间要相互连通，需满足三个条件：
1. 网络是相通的；
2. 使用相同的genesis.json初始化节点；
3. 启动节点时，--networkid选项指定相同的networkid。

现有两个节点，节点1与节点2，networkid都是20180908，通过下面的步骤建立连接。
1. 在节点1控制台使用admin.nodeInfo.enode获取enode信息
```
> admin.nodeInfo.enode
"enode://cfbed48b370aa24e31728459c45f4e0751026ce678bfebb618743bb9db37cb3104d50b8a1db6d8f435bfb7b8b6ca033389f1a336a75f708b07b4c96dd54effd8@[::]:60303"
```
2. 在节点2控制台使用admin.addPeer()加载节点1的enode信息
```
> admin.addPeer("enode://cfbed48b370aa24e31728459c45f4e0751026ce678bfebb618743bb9db37cb3104d50b8a1db6d8f435bfb7b8b6ca033389f1a336a75f708b07b4c96dd54effd8@172.17.0.12:60303")
true
```
3. 等待两个节点连接成功后，节点2就会开始同步节点1的区块，同步完成后，任意一个节点开始挖矿，另一个节点会自动同步区块。
