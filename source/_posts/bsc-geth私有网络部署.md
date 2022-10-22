---
layout: post
title: bsc parlia geth私有网络部署
date: 2021-11-29 17:35:20
tags: blockchain
categories: note
---

### 如何部署parlia geth私有网络节点

#### 改动关键点

1. 出块的验证者节点
   -  创世区块的默认验证者 （第一个epoch前出块的账户）
   - 系统合约中的初始化验证者（区块高度每到达一个epoch之后，会查询系统合约 validatorSet contract 获得当前的验证者）
2. 中继者注册资金
   - 编译relayerHub合约，将初注册资金改为0，编译合约放置在创建文件中
3. chainID 私有网络id
4. alloc初始余额分配情况



具体启动节点步骤：

1. clone 系统合约项目（官方地址：git@github.com:binance-chain/bsc-genesis-contract.git ，下列仓库有做过验证者集合genesis-validator.js 支持docker编译的优化）

   ```shell
   git clone git@github.com:aranoverse/bsc-genesis-contract.git
   ```

2. init_holder.js：该文件初始化创世区块中的账户资产持有情况。

   ```JavaScript
   const web3 = require("web3")
   const init_holders = [
     {
       // private key is 0x9b28f36fbd67381120752d6172ecdcf10e06ab2d9a1367aac00cdcd6ac7855d3, only use in dev
       address: "0x9b28f36fbd67381120752d6172ecdcf10e06ab2d9a1367aac00cdcd6ac7855d3",
       balance: web3.utils.toBN("10000000000000000000000000000000").toString("hex")
     }
   ];
   exports = module.exports = init_holders
   ```

3. validators.js: 初始化验证者 投票权重之类

   ```javascript
   // Configure
   let validators = [
       {
           consensusAddr: "0x9fB29AAc15b9A4B7F17c3385939b007540f4d791",
           feeAddr: "0x9fB29AAc15b9A4B7F17c3385939b007540f4d791",
           bscFeeAddr: "0x9fB29AAc15b9A4B7F17c3385939b007540f4d791",
           votingPower: 0x0000000000000064
       }
   ];
   ```

4. 根据需求改造genesis-template.json 或者 生成之后的genesis.json

   ```shell
   #生成genesis.json 以及编译系统合约
   node generate-genesis.js  
   ```

5. config配置参考https://geth.ethereum.org/docs调节，或者以下

   ```toml
   [Eth]
   NetworkId = 12345
   NoPruning = false
   NoPrefetch = false
   LightPeers = 100
   UltraLightFraction = 75
   TrieTimeout = 100000000000
   EnablePreimageRecording = false
   EWASMInterpreter = ""
   EVMInterpreter = ""
   
   [Eth.Miner]
   GasFloor = 30000000
   GasCeil = 40000000
   GasPrice = 1000000
   Recommit = 10000000000
   Noverify = false
   
   [Eth.TxPool]
   NoLocals = true
   Journal = "transactions.rlp"
   Rejournal = 3600000000000
   PriceLimit = 1000000000
   PriceBump = 10
   AccountSlots = 512
   GlobalSlots = 10000
   AccountQueue = 256
   GlobalQueue = 5000
   Lifetime = 10800000000000
   
   [Eth.GPO]
   Blocks = 20
   Percentile = 60
   OracleThreshold = 20
   
   [Node]
   IPCPath = "geth.ipc"
   HTTPHost = "0.0.0.0"
   NoUSB = true
   InsecureUnlockAllowed = false
   HTTPPort = 8545
   HTTPVirtualHosts = ["*"]
   HTTPModules = ["eth", "net", "web3", "txpool", "parlia"]
   WSPort = 8546
   WSHost = "0.0.0.0"
   WSModules = ["net", "web3", "eth"]
   
   [Node.P2P]
   MaxPeers = 30
   NoDiscovery = false
   #BootstrapNodes = [""]
   #StaticNodes = [""]
   ListenAddr = ":30311"
   EnableMsgEvents = false
   
   [Node.HTTPTimeouts]
   ReadTimeout = 30000000000
   WriteTimeout = 30000000000
   IdleTimeout = 120000000000
   
   [Node.LogConfig]
   FilePath = "bsc.log"
   MaxBytesSize = 10485760
   Level = "info"
   FileRoot = ""
   
   ```

   

6. 利用genesis.json 初始化，然后运行

   ```shell
   # Linux
   wget  https://github.com/binance-chain/bsc/releases/download/v1.1.5/geth_linux
   # MacOS
   wget  https://github.com/binance-chain/bsc/releases/download/v1.1.5/geth_mac
   
   # 最少需要两个在线验证者节点 在第3步中更改
   ./geth  --datadir ${data_dir_1}  init genesis.json
   ./geth  --datadir ${data_dir_2}  init genesis.json
   
   ./geth --config ./config.toml --datadir ${data_dir_1}  --syncmode snap -unlock ${validator_account_1} --password ${passwd_txt_location} --mine --allow-insecure-unlock --cache 8000 
   
   # e.g. 
   nohup ./geth --config ./config.toml --datadir ./node1  --syncmode snap -unlock ${addr} --password ./password.txt --mine --allow-insecure-unlock --cache 8000 --verbosity 5  --port  30011 --http.port 8545 --ws.port 8546  > ./node1/node.out 2>&1 &
   nohup ./geth --config ./config.toml --datadir ./node2  --syncmode snap -unlock ${addr} --password ./password.txt --mine --allow-insecure-unlock --cache 8000 --verbosity 5  --port  30012 --http.port 8547 --ws.port 8548   > ./node2/node.out 2>&1 &
   ```

7. 添加到同一网络

   - 方式一：手动添加

     ```shell
     #在节点A拿到enode信息
     ./geth attach ${datadir}/geth.ipc
     admin.nodeInfo.enode
     #节点B添加
     ./geth attach ${datadir}/geth.ipc
     admin.addPeer(${enode1})
     ```

   - 方式二：启动一个全节点后拿到enode信息，在启动其他节点之前把config.toml中的BootstrapNodes 添加第一个节点的信息