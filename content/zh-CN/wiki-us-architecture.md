---
id: wiki-us-architecture
title: 系统架构
sidebar_label: 系统架构
---

区块链网络正在分层化和专用化，基础的公链网络负责共识安全性和数据, 资产的跨链，第二层网络和侧链则正在往特定应用领域发展。

类似波卡网络和 Substrate 这样的新技术发展，符合了这样的业态发展趋势，在这种背景下，达尔文网络(Darwinia Network)，一个专注于资产互联的跨链网络，选择加入这个生态和技术趋势，将分层网络、跨链交互、面向应用设计、用户体验等作为我们的关键设计特性和原则。

在使用区块链技术打造新型区块链应用的过程中，我们发现区块链技术的大规模推广还存在几个问题:

### 当前的区块链基础设施还无法满足用户体验

目前区块链应用的用户体验问题主要体现在两个方面，一是数字钱包的使用上手困难，助记词需要备份和忘记密码无法找回资产对于用户来说还是很大的认知门槛和使用门槛。二是由于目前公链的低 TPS，以及燃料费付费对于习惯互联网免费的用户来说也是比较大的障碍。

### 传统 IT 和互联网公司缺乏区块链应用开发工具和方法

区块链应用的开发需要一定的区块链技术积累，传统应用开发者搭建一套完备的区块链开发平台成本较高。

### 不同公链之间的区块链应用是割裂的

由于公链的异构，区块链应用开发者为了触及多个公链的用户，需要为每一个公链重复开发同一款应用，成本比较高。

我们希望使用目前最先进的区块链技术和框架来构造一个开放的网络和应用套件来解决这些问题。这个网络和应用套件将区块链可信技术和 Web3 基础设施，同时又具备以下特性，即分层网络设计，支持跨链交互，开发者友好，最佳用户体验，高并发可定制。

### 架构设计

达尔文网络是基于 Substrate 技术构建的区块链网络，作为 Polkadot 生态的一员，同时又区分于 Polkadot 的是，达尔文网络是一个桥接网络，并且主要专注于资产兑换协议方向的跨链业务。

通过达尔文网络，区块链应用可以通过达尔文网络方便的进行资产的跨链交互和交易，比如，以太坊上的谜恋猫 (Cryptokitties) 游戏可以通过达尔文链把以太坊上的 NFT : 谜恋猫转变成 EOS 上的谜恋猫；以太坊上的玩家和 EOS 上的玩家可以通过达尔文网络同时玩进化星球游戏。同时得益于 Polkadot 生态，达尔文网络可以链接更广泛的游戏和玩家。达尔文网络生态关系如下图所示

![Architecture](assets/architecture-cn.png)
