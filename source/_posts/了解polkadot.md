---
layout: post
title: 了解polkadot
date: 2022-01-12 16:49:54
tags: blockchain
category: note
author: 
  nick: Vincent
  link: bloger.top
---

# 波卡

## polkadot是一条基于substrate创建的主链，又称为中继链

### 为与其连接的平行链，提供安全性，流通性

## parachain 平行链

### 与波卡连接的其他链，他们可以有自己的方式产生区块

## shared security

### 1. 验证者出块，且不出快的验证者会受到惩罚

### 2.平行链也间隔性的发送候选区块，中继链会将该区块也提交到中继链中

## forkless upgrades and governance

###  on-chain runtime

- 生成新区块的逻辑的代码放置于链上

### on-chain governance

- 任何持有DOT的用户可以进行公投，根据投票情况polkadot自动升级更新

![polkadot](https://raw.githubusercontent.com/arano9/pic-host/main/img/polkadot.png)
