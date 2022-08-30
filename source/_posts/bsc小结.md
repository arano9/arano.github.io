---
layout: post
title: bsc小结
date: 2021-11-18 21:25:15
tags: blockchain
categories: note
---

# bsc

## 共识

### 目标

- 确定区块更少，大多情况不会有分叉
- 出块时间快于ethereum1.0  <=5s
- 没有通胀，区块奖励就是交易gas fee
- 与以太坊一样兼容
- 拥有和cosmos一样的质押和治理

### 实施

- bc为bsc做质押与治理
- bsc保持类似clique系统的PoA

### 架构

- bc通过质押程序进行选取流程，来决定bsc的validators
- bsc validators 验证交易以及出块，确保网络安全以及账本一致性，获得 gas fee作为奖励
- staking dapp on bsc（system contract）

  - light client contract：仅用于验证bc tendermint 共识状态验证
  - cross chain contract：跨链沟通层，验证 跨链包的merkle proof 以及sequence
  - bsc validator contract: watcher of bsc validators change on bc，存储验证集的奖励gas fee，分发验证集改变的跨链包奖励

    - handleSynPackage(uint8, bytes calldata msgBytes)

    - CurrentValidator() returns ([]address)：返回没有被标记监禁的验证者
    - deposit(address valAddr) external：分发奖励给验证者

  - system reward contract：奖励中继者维护系统的合约

    - claimRewards(address payable to, uint256 amount) external：转账bnb

  - liveness slash contract：记录验证者错过的区块指标，一旦指标超过阈值，出块奖励被更好的验证节点共享

    - Slash(validator address) external：
      1、验证者 missing block 指标+1
      2、如果指标到了50将使用 bsc validator contract的函数 将奖励分发给其他验证者
      3、如果指标到达了150 不仅调用函数分发奖励，并且将该验证者剔除验证者集合中

  - other contract：提供 bc的治理功能给bsc使用

### 奖励分发：高度可配置

- 奖励规则：全部来源于transaction fee

  - 出块的validator可收到15/16 of gas fee
  - system reward contract:收到 1/16 of gas fee(如果持有余额大于100BNB将不会得到)

### 协议：与clique协议类似（https://ethereum-magicians.org/t/eip-225-clique-proof-of-authority-consensus-protocol/1853）

- key diff feature

  - light client security：利用epoch（200 blocks）+N/2 保证轻量级客户端的安全性
  - system transaction： 共识系统调用sys contract生成的交易，由验证者签名，见证节点也会生成交易（无签名）与其比对然后应用
  - enfore backoff：避免验证者为获奖励而急于出块，过早出块将会被见证节点丢弃

### 产生新区块

- prepare: 
  1、load snapshot
  2、If (height % epoch)==0, fetch validators from bscValidatorSet contract 
  3、store validator-set-msg with extraData the filed of block header
  4、conbase addr：validator’s addr
- finalize and assemble
  1、如果验证者不是该轮的验证者，调用slash contract生成slash交易
  2、如果有gas fee，分发1/16 system reward contract 剩余的奖励验证者
- seal 签戳 before broadcast
  1、sign in block header ，添加签名到extraData
  2、非该轮的验证者签名，诚实的验证者会随机等待一会儿

### 验证/中继 区块

- 验证block header
  1、验证signature of coinbase 
  2、验证区块时间是否比期望时间小，可以阻止自私的验证者急于打包
  3、coinbase 应该是signer 难度应该是期望值
- finalize
  1、如果是epoch block，验证者会从验证集中比较 extra data
  2、如果区块不是这轮验证者生成，将会调用 slash contract，如果有gas fee，分发1/16 system reward contract 剩余的奖励验证者。
  3、交易经过共识机制生成的交易必须和块中交易一致

### 签名

### 安全与最终性

- BC 鼓励用户一个块经过以下时间可保证安全和最终性（2n/3+1)*block time ,利用slashing logic 增强以后(n/2+1)*block time已经就可保证

## 创世

### initialization

1、启动时必须有BNB在bsc
2、所有初始的验证者必须记录在bc

## 钱包

### similar way to etherum，BIP39 ，Binance Ext Wallet

## 跨链

### system contract

###  relayer：

提交cross-chain  communication package

- bsc relayer：bc to bsc
  1、可被任何人执行，独立进程
  2、relayer必须在bsc注册，且存款质押BNB
- oracle module on bc: 验证bsc relayer传过来的交易
- oralce relayer : monitor 一些在bsc的事件，广播交易到bc上，relayer 需要有验证者的私钥权限，每个validator一个relayer服务
- 激励机制：

![bsc](https://raw.githubusercontent.com/arano9/pic-host/main/img/bsc.png)

