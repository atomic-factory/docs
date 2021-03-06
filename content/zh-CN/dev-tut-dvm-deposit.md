---
id: dev-tut-dvm-deposit
title: DVM 地址充值
sidebar_label: DVM 地址充值
---

> 本教程所示操作，目前只在 Pangolin 测试网络中使用。

## Pangolin 测试网络

Pangolin 测试网络是 Darwinia 团队最近开放的一个测试网络，代币名称 `PRING`。该网络集成了 DVM(Darwinia Virtual Machine) 智能合约解决方案，将在目前的 darwinia 网络中支持以太坊智能合约，并且兼容多数以太坊合约基础设施 Metamask， Remix 等。不同于 Crab 测试网络，为了方便测试合约, 合约开发者可以免费申请获取一定数量的 Pring。

需要测试币的开发者，可以在 [Darwinia Element](https://app.element.io/?pk_vid=6961ca0f7c45f8bf16052310122d2437#/room/#darwinia:matrix.org) 中与相关工作人员联系。

## Metamask 增加 Pangolin 网络

首先在 Metamask 增加对 Pangolin 网络的支持，操作如图所示：

![增加网络](assets/wiki-tut-dvm-recharge-01.png)

![配置网络](assets/wiki-tut-dvm-recharge-02.png)

配置项说明：

- 网络名称：`Pangolin`
- RPC URL: `https://pangolin-rpc.darwinia.network`
- 链 ID： `43`
- 符号： `PRING`

单击保存，pangolin 网络便添加成功。之后就可以使用 matamask 来进行 EVM address 之间的转账，合约部署等。

## Apps 增加 Pangolin 网络

Apps 是 Darwinia 官方提供的工具包集合，切换到 Pangolin 网络的操作如下， 单击 https://apps.darwinia.network/，并增加自定义终端配置
`ws://t1.hkg.itering.com:9944/`，点击保存并加载即可。

![增加 apps 支持](assets/wiki-tut-dvm-recharge-03.png)

> 注：如果提示 `websocket unsecurity warn`。 请修改 Chrome 配置，`设置` - `不安全的内容` - `允许`。

## DVM 地址充值

### 准备工作

- EVM address（Metamask 生成即可）
- Darwinia address （需要有一定余额，社区领取）

### 充值

1. 生成 EVM address 对应的 Darwinia address

在 `Apps` - `工具箱` - `DVM 地址` 里输入 EVM address，将会生成对应的 Darwinia address，该地址将代表 EVM address 接收 PRING.


![create substrate address](assets/wiki-tut-dvm-recharge-04.png)
`0xAa01a1bEF0557fa9625581a293F3AA7770192632` 对应的 Darwinia address 为 `5ELRpquT7C3mWtjerXnTxDmKnvVxJjCCstXcN8yG34o4365H`


2. 使用 [Apps](https://apps.darwinia.network/#/account) 转账

Apps 内转账，from 地址为余额不为 0 的账户地址，target 地址填写由 EVM address 生成的 Darwinia address, 这里是 `5ELRpquT7C3mWtjerXnTxDmKnvVxJjCCstXcN8yG34o4365H`。

![transfer pring](assets/wiki-tut-dvm-recharge-05.png) 发送转账交易并等待确认。


3. Metamask 确认充值成功

![confirm balance in matamash](assets/wiki-tut-dvm-recharge-06.png)

EVM address `0xAa01a1bEF0557fa9625581a293F3AA7770192632` 的余额为 100，充值完成。
