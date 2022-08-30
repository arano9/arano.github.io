---
layout: post
title: OlympusDao
date: 2021-12-21 10:28:59
tags: blockchain
category: note
---

# Olympus Dao

## what is this

### OHM算法储备货币：一系列资产支持，调节供给来维持OHM的购买力

### 稳定币

不与美元挂钩
价格自由浮动，市场决定
资产支撑，DAI、FRAX...

## goal

### 短期

- 实现OHM供给增长并且优化财富创造体系

### 长期

- 使用OHM作为全球流通货币，基本记账单位

## participation

### 质押

- 来源1

  - 二级市场购买（uniswap，sushiswap）

    - 用户直接质押OHM，获得1比1 staked OHM， 每8h发放一次奖励 （该过程称为rebase）

- 来源2

  - 债券 bonding

    - 用户存入特定资产，折扣价获得OHM，5天后获得15个rebase（过程不可逆，即不可兑换为原资产）

      - 特定资产

        - 储备债券（reserve bonding）：存入DAI、FRAX或者LUSD换取OHM
        - 流动债券（liquidity bonding）: 存入uniswap 或者 sushiswap上的LP（如：OHM-DAI LP...）换取OHM

## operating mechanism

### 债券机制

- OHM供给调节机制：协议每铸造一个OHM，需一个DAI做支持。当OHM价格小于$1，协议会自动回收OHM直到大于$1。OHM价格理论上无上限的大于1

OHM价格 = 1 Dai + premium(溢价)

例如：如果OHM价值$1100 , 用户在sushiswap 组OHM-DAI SLP 值$1000，然后将其存入流动性债券；协议收到SLP后存入国库，计算RFV为$
15。每个OHM有1DAI支持，于是协议制造15个OHM，其中1个给用户，14的90%分给质押者，剩余的留存国库；相当于用户9折价格买到OHM，但是只能5天后到账；对于协议来首，收到了$1000资产，对其进行减值，铸造了响应数量OHM


	- RFV计算方式
	
		- LP计算方式：RFV=2sqrt(constantProduct)*(% ownership of pool)
		- 储备债券： RFV=assetSupplied
	
	- POL protocol owned liquidity： 协议自有流动性

### 质押机制

- OHM价格=1Dai+premium(溢价)

![OlympusDao](https://raw.githubusercontent.com/arano9/pic-host/main/img/OlympusDao.png)
