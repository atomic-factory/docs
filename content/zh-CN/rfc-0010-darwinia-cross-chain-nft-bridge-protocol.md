---
id: rfc-0010-darwinia-cross-chain-nft-bridge-protocol
title: 0010 Darwinia Cross Chain Nft Bridge Protocol
sidebar_label: 0010 Darwinia Cross Chain Nft Bridge Protocol
custom_edit_url: https://github.com/darwinia-network/rfcs/edit/master/RFC/zh_CN/0010-darwinia-cross-chain-nft-bridge-protocol.md
---

- rfc: 10
- title: 0010-darwinia-cross-chain-nft-bridge-protocol
- status: Draft
- desc: Cross-chain NFT Bridge Protocol


# Cross-chain NFT Bridge Protocol			

###  v 0.3.0



## I. 概述

基于 XClaim 的框架给通证跨链提供了一个思路，但是对于 NFT 仍有很多问题，其中主要包括 Backing Blockchain 上 Vault 抵押物设计的问题。通过在 Backing Blockchain 上引入 chain-Relay 合约可以有效的解决这个问题，本文将基于这个改进的跨链转接桥方案，设计跨链 NFT 的方案和标准。

**关键词**:Blockchain, NFT, cross chain, multi-chain



## II. 背景

### A. 研究历史

比特币[1]的出现，允许每个人只要拥有私钥，就可以不依赖任何信任地操作自己的资产。整个比特币系统，由一系列记录着自己前序区块 hash 的区块构成，共同维护着同一份去中心化的全球“账本”。

比特币的出现之后，紧接着的就是区块链的飞速发展，出现了支持智能合约的公链——以太坊[2]，PoS 的公链——EOS[3]等。这些公链的爆发，带来了整个 token 交易市场的繁荣。

主流的 token 交易/交换方式仍然是中心化交易所，用户的 token 由中心化交易所代为管理。信任和维护成本很高，并且还需要面临源源不断的黑客攻击的威胁。

为了克服中心化交易所的弊端，去中心化交易所 (DEX) 开始涌现。绝大部分去中心化交易所只支持在一条链上进行链内的 token 交易/转换，比如以太坊上的 ERC20[4], ERC721 token[5]. 这一定程度上实现了去中心化，降低了信任成本（从相信机构变成了相信代码），但是使用场景十分有限，并且还要受限于公链的 tps 和交易费用。

当然也有一部分的去中心化交易所实现了 ACCS，允许 token 跨链交换。它们使用了 hashed timelock contracts (HTLCs)[6].  HTLCs 的优点同它的缺点一样，都很明显。HTLCs 可以在不需要信任的情况下实现跨链 token 的原子交换，这既实现了去中心化，又拓展了单条链上的 DEX 的功能。它的缺点就是成本太高，并且限制条件很多：(i) 所有参与方都必须保持全过程在线  (ii) 对粉尘交易失效  (iii) 通常锁定时间较长。这样的 token 跨链交换既昂贵又低效。在实际使用中，HTLCs 的使用范例也非常少。

为了实现去信任的、低成本的、高效率的 token 跨链操作，XClaim 团队提出了 cross claim 方案，基于 CBA。并且在 XClaim 的论文中详述了 XClaim 是如何完成以下四种操作的：Issue, Transfer, Swap and Redeem.

 XClaim 系统中保证经济安全的角色被称为 $vault$,  如果任何人想要把 chain $B$ 上的原生 token $b$ 跨到 chain $I$ 变成 $i(b)$ ，那么就需要 $vault$ 在 chain $I$ 上超额抵押 $i$ 。 在赎回操作中，如果 $vault$ 存在恶意行为，则罚掉 $vault$ 抵押的 $i$ ，用于赎回操作发起者。其他细节详见 XClaim 的论文[7]。

至此，对于流动性较好的 Fungible token 的跨链，已经得到一个可靠的、可实现的方案。

### B. XClaim 框架存在的问题

XClaim 方案中有着一个基本假设，即跨链锁定的 chain $B$ 的原生 token $b$ 的总价值， 与在 $I$ 上发行出的 $i(b)$ 的总价值相等，在 XClaim 中被称为*symmetric*, 即 $\|b\| = \|i(b)\|$。这样的假设是的 XClaim 在 NFT 的跨链中存在着天然的困境：

- NFT 的不可替代性。正因为 NFT 具有可识别性、不可替代性等特点，使得 $vault$ 在 chain $I$ 上抵押 chain $B$ 上的 NFT $nft_b$ 成了一件不可能的事情。
- NFT 的价值难以评估。在 XClaim 中，判断 $vault$ 的抵押是否足额/超额，是通过 Oracle $O$ 实现的。这也存在一个潜在的假设：token $b$ 和 token $i$ 可以被正确地评估价值。基于目前繁荣的中心化和去中心化交易所，在提供了良好的流动性的情况下，是可以基本满足该潜在假设的。但是 NFT 交易所市场尚未成熟，即使中心化交易所也无法比较真实地反应市场对 NFT 的价格判断。NFT 如何定价本身就是一个难题。
- NFT 定价不具有连续性和可预测性。即使某个 NFT 在市场上有了一次成交记录，即有了一个价格，因为 NFT 被售出的频次远低于 FT，即使在市场流动性非常好的情况下，该 NFT 下一次的成交价格既不连续，也不可预测。

### C. 解决方案和思路

解决以上问题的 NFT 跨链方案有两种思路，一种是基于 XClaim 框架并保留桥质押机制的的 NFT 扩展，通过引入哈伯格机制来解决 NFT 定价问题，详细的解决方案见[RFC-0011: Using Harberger Tax to find price for XClaim Vault Collaterals](./rfc-0011-using-harberger-tax-to-find-price-for-xclaim-vault-collaterals). 但这个方案仍然无法很好的解决由于 NFT 价格变化太大，导致的质押物不足问题。

另一个思路是通过在 Backing Blockchain 引入 chainRelay 的方案，对背书的资产做更多的保护，使得不再需要质押机制，简称为[RFC-0012: Darwinia Bridge Core: Interoperation in ChainRelay Enabled Blockchains](./rfc-0012-darwinia-bridge-core-interoperation-in-chainrelay-enabled-blockchains)，详细的介绍将不在本文进行详细介绍，本文将着重基于这个改进的跨链转接桥方案，设计一个跨链的 NFT 标准，并且在多链互跨的情况下，提出了更低成本、功能具备扩展性的跨链协议。



其中，在[RFC-0012](./rfc-0012-darwinia-bridge-core-interoperation-in-chainrelay-enabled-blockchains) V.A 中，我们引入了 Darwinia Bridge Core 的模型，用来优化区块链网络拓扑中的 chainRelay 数量。本文将基于 Darwinia Bridge Hub，并针对 NFT 特定领域的问题，进行细化设计。

<img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g7fe8rjjzvj30kb0bfgmc.jpg" alt="chain-relay-framework" style="zoom:80%;" />

## III. NFT in Darwinia Bridge Core

NFT 跨链操作的难点在于，不同的公链有着自己的 NFT 标准，甚至不同公链上的 NFT 的 token id 连长度都是不相等的，NFT 在跨到不同公链时，必然会经历 token id 的转换。如何在跨链的过程中不丢失 NFT 的可识别性，是一个值得研究的命题。

在设计 Bridge Core 内的 NFT 流转逻辑时，我们想解决以下三个问题：

- 保留 NFT 的跨链流转路径/历史，不损失 NFT 的可识别性；
- 计算和验证解耦，拥有更高的处理速度；
- 实现额外功能，例如 NFT 在跨链的同时完成分解、合并等操作；



为此，我们选择为每个经过 Bridge Core 跨链的 NFT 引入一些中间解析状态，称为 UNFO (Unspent NFT Ouput)，这些 UNFO 状态将维护一个 Bridge Core 上全局的 ID，并借由跨链流通证明记录全局 ID 和 NFT 外部本地 ID 的映射关系。UNFO 并不一定具体负责该 NFT 在 Bridge Core 范围内的所有权管理(Ownership Management)，但也可以借由一个 $lock\_script$ 进行扩展，例如通过将 $lock\_script$ 指向 Bridge Core 内部的一个所有权管理合约。

<img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g7fe8qjwd9j30fe0gh3zg.jpg" alt="0010-framework-of-bridge-core" style="zoom:50%;" />



### A. 组件定义

- *Issuing Smart Contract*,  $iSC_N$:  表示在 chain *N* 上的资产发行合约；
- *Backing Smart Contract*,  $bSC_N$ : 表示在 chain $N$ 上的资产锁定合约；
- *Verifying Smart Contract*,  $vSC_N$ : 表示在 Bridge Core 上负责验证 chain *N* 上交易的资产发行合约/模块；
- *Global identifier* ,  $GID$ , The global idendifier for the NFT in Darwinia Bridge Core
- *Unspent Non-Fungible Output* ,  $UNFO$, Intermediate Resolution State for the NFT in Darwinia Bridge Core, aka. unspent NFT output. 该想法源于 UTXO，当一个 UNFO 被销毁时，意味着同时会产生一个新的 UNFO.
- *External Backing NFT*,  $nft_B^{x,n}$,  表示在 chain $B$ 上，在合约 $x$ 中标识为 $n$ 的 NFT
- *Bridge Core Mirror for Backing NFT*, $nft_{BC(unfo_{gid})}^{B,x,n}$,  或简称 $nft_{BC}^{B, n}$ ，跨链到 Bridge Core 中的中间状态的 NFT, 并且和 chain $B$ 上的 $nft_B^{x,n}$ 互为镜像，表示在对应 chain $B$ 中有一个即将被发行/已锁定的 NFT.  $unfo_{gid}$ 表示该 NFT 在 Bridge Core 内的中间态 UNFO.
- *External Issueing NFT*,  $nft_I^{x',n'}$,  表示跨链后在 chain $I$ 上新增发的、在合约 $x'$ 中标识为 $n'$ 的 NFT
- *Bridge Core Mirror for Issuing NFT*,$nft_{BC(unfo_{gid})}^{I,x',n'}$, 或简称 $nft_{BC}^{I, n'}$ ，跨链到 Bridge Core 中的中间状态的 NFT, 并且和 chain $I$ 上的 $nft_I^{x',n'}$ 互为镜像，表示在对应 chain $I$ 中有一个即将被发行/已锁定的 NFT.  $unfo_{gid}$ 表示该 NFT 在 Bridge Core 内的中间态 UNFO.
- *Locking Transaction* ,  $T_{B}^{lock}$,  在 chain *B* 上把 NFT 锁定在 $bSC_B$ 中的交易
- *Redeem Transaction* ,  $T_I^{redeem}$， 在 chain *I* 上把 NFT 锁定在 $bSC_I$ 中的交易
- *Extrinsic Issue*,  $EX_{issue}$ , Bridge Core 上的 issue 的交易 
- *Extrinsic redeem*,  $EX_{redeem}$ , Bridge Core 上的 redeem 的交易 

参与方：

- *validator*,  维护 Bridge Core 的参与方；

### B. UNFO 实现和作用

#### B.I UNFO 的数据结构

```rust
struct UNFO {
  pub local_id, // chain_id + smart_cotnract_id + token_id
  pub global_id,
  pub phase, // current phase
  pub lock_script, // e.g. ownership or state management
}
```

- **local_id**:表示该 UNFO 对应着某个外部区块链 *chain_id* 上某个 *smart_contract_id* 里的 *token_id*
- **global_id**:表示该 UNFO 在 Bridge Core 和所有被夸的区块链范围内的全局唯一标识
- **phase**:表示该 UNFO 在跨链过程中所处的阶段。比如：
  - 1: 该 UNFO 对应区块链 *chain_id* 上的 NFT 被锁定/销毁；跨链过程处于中间状态；
  - 2: 该 UNFO 对应区块链 *chain_id* 上的 NFT 待发行/已发行；跨链过程即将完成/已完成
- **lock_script**:用于更加复杂逻辑、细粒度的控制脚本，保持 UNFO 的可扩展性。lock_script 表达的是这个 NFT 的所有者是谁，当该 NFT 在 Bridge Core 之内流转时，该 lock_script 指向的可能是某个 ownership contract，当 NFT 被锁定在 backing contract 里面时，lock_script 指向的可能是 backing contract 的 redeem 合约



#### B.II. UNFO 的转换

我们选择用 UNFO 模型作为存储/状态 的流转单元，UNFO 模型是一种类似 UTXO 模型的设计思路。

当一个 UNFO 的销毁，意味着另一个 UNFO 的创建，如果我们追溯 UNFO 的销毁创造历史，就可以回溯某个 NFT 的全部跨链历史，这一定程度上帮助实现了 NFT 的可识别性；

每个 UNFO 只能被销毁一次，这使得计算前不一定要先验证，从而提高了处理速度；

正如比特币的 UTXO 一样，Input 和 Output 都可以有多个，这样的特点使得 NFT 在跨链的过程中，可以同时完成一些扩展功能，例如 NFT 的拆分和合并。

一直一来，NFT 都比 FT 有用更多的操作种类，例如在游戏中，作为道具的 NFT 要求可拆解、可合成、可升级等，为此扩展出了很多 NFT 标准，例如 ERC721, ERC1155, ERC721X 等。标准越多，越难被广泛使用。

如果其中的一些通用需求可以在跨链同时实现，可以有效地减少标准的数量和冗余度，一定程度上更有利于实现一个统一的标准。



当一个 UNFO 产生时，一定要满足：

- 提供 *backing blockchain* 的 对应 NFT 的锁定记录；
- 另一个 UNFO 被销毁
  - 条件：销毁和产生的 UNFO 的 GID 必须相同

<img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g7fe8skd28j30hj06z0sz.jpg" alt="0010-UNFO-transform" style="zoom:50%;" />

#### B.III. 基于 UNFO 的 NFT 映射和 ID 一致性

Fungible Token 在跨链时只要保证 CBA 和原生资产价值对称、资产安全即可，但是 NFT 对可识别性有更高的要求，因此需要再跨链时更好的维护 NFT CBA 和原生资产的一一映射，并保持 ID 的一致性，包括 GID 和 External Locol ID。

| UNFO | GID     | External Chain ID | External Contact Address | External Token ID | Lock_Script                  | Active Status |
| ---- | ------- | ----------------- | ------------------------ | ----------------- | ---------------------------- | ------------- |
| 1    | GID0001 | Ethereum          | A_ERC721                 | 12                | script_issuing_burn_or_relay | False         |
| 2    | GID0001 | Tron              | B_TRC721                 | ?                 | script_backing_redeem        | True          |
| 3    | GID0002 | EOS               | C_dGoods                 | 2.5.4             | script_issuing_burn_or_relay | False         |

<center> 基于 UNFO 的映射表示例 </center>
假如我们用 nft(Exterenal_Chain_ID, External_Contract_Address, External_Token_ID)标识一个外部公链上的 NFT。在没有 NFT 的跨链映射表的情况下：

>  nft(A, X, 1) 表示在 A 链上、合约 X 中标识为 1 的 NFT。

在没有 NFT 的跨链映射表的情况下，

> Alice 在跨链桥 M(A->B)中，将 nft(A, X, 1) 变为 nft(B, Y, 2) ；又通过跨链桥 N(B->C)，将 nft(B, Y, 2) 变为 nft(C, Z, 3)。之后，当 Alice 想继续使用跨链桥 M 将 C 链上的 nft 跨链去 A 链的话，因为没有跨链映射表查询该 NFT 在 A 中的 ID 信息，跨链桥 M 会将 nft(C, Z, 3) 识别为新的 NFT CBA，这样很可能在跨回 A 链时，将不再是 nft(A, X, 1)，而是 nft(A, X, 5). NFT 就丢失了自己的可识别性和 ID 一致性，会给协议和赎回过程带来额外的复杂性和混乱。

为了尽可能减少丢失 NFT 可识别性对用户造成的潜在资产损失，如果 NFT 在 Bridge Core 连接的网络之内跨链，则用户可以获取到 Bridge Core 上某个 NFT 当前的基于 UNFO 的 NFT 映射表，协议将可以约束用户跨链回 A 链的 NFT 需要遵循 ID 一致性和可识别性。这样，至少在 Darwinia Bridge Core 系统内，NFT 可保证可识别性不被破坏。

为了保持良好的 ID 一致性，在 NFT 通过 Bridge Core 跨链流转的生命周期内，希望保持 External Chain ID 和 (External Contact Address, External Token ID) 的映射关系保持不变，此时可以通过基于 UNFO 的解析服务，至 UNFO 映射表里面查询相应的 External Token ID，以保持一致性。在 RFC-0013 章节 III.D 中，我们还将详细介绍 NFT 解析模块。

### C. 初步实现方案

场景同章节 II 中的描述。依然需要实现三种 protocol:*Issue, Transfer, Redeem*. 同样为了简化模型，这里将不会讨论手续费相关细节。

#### Protocol: Issue

(i) ***锁定***。*requester* 将 chain $B$ 上的 NFT 资产 $nft_B^{n,x}$ 锁定在 $bSC_B$ 中，同时声明目的地公链 chain *I* 以及自己在 chain $I$ 上的地址；这一步将产生交易 $T_B^{lock}$

(ii) ***Bridge Core 上发行***。 *requester*  将锁定交易 $T_B^{lock}$ 提交至 Bridge Core, 对应的 chain relay 验证通过后，即 触发 $vSC_B$ , 在 $vSC_B$ 中：

- 产生新的 $GID$ 和 $nft_{BC}^{B,n}$ , 记录 $GID$ 和 $nft_{BC}^{B,n}$ 二者之间的关系，
- 并触发 $vSC_I$ 

在 $vSC_I$ 中：

-  销毁  $nft_{BC}^{B,n}$，发行 $nft_{BC}^{I,?}$， $issue\_{ex}(\ GID,\ address\_on\_I) \rightarrow EX_{issue}$

(iii) ***发行***。*requester* 将 $EX_{issue}$ 提交至 chain $I$ , 经过 chain $I$ 上的 chain relay 验证通过后，即会在 $iSC_I$ 中增发新的 NFT: $nft_I^{x', n'}$， 并记录 $GID$ 和 $nft_I^{x', n'}$ 的关系， 且将所有权交给 *requester* 在 chain *I* 上的地址

注: 对于外部区块链上的 $iSC$ 来说，在发行时，也需要在外部区块链上，将全局 ID 和本地 ID 的映射记录下来，因为后面 redeem 的时候，需要使用这个映射关系来完成 redeem.

<img src="https://tva1.sinaimg.cn/large/006y8mN6gy1g7sznhszi8j30pz0elabd.jpg" alt="chain-relay-framework-1" style="zoom:50%;" />

#### Protocol: Transfer

(i) ***转移***。*sender* 在 $I$ 上把 $nft_I^{x',n'}$ 在  $iSC_I$ 中，把所有权转移给 *receiver*，参考 ERC721.

(ii) ***见证***。当 $nft_I^{x',n'}$ 在  $iSC_I$ 中的所有权发生了转移时，$iSC_I$ 和 $bSC_I$ 都应当觉察。此时，当 *sender* 再想把 $nft_I^{x',n'}$ 赎回时需要先将其锁定在 $bSC_I$ 中，此时 $bSC_I$ 将不会允许该操作成功。

#### Protocol: Redeem

(i) ***锁定***。 *redeemer* 将 chain $I$ 上的 NFT 资产 $nft_I^{x', n'}$ 锁定在 $bSC_I$ 后 (如果有对应的 GID，锁定时需声明 $GID$)，同时声明目的地公链 chain $B$ 以及自己在 chain $B$ 上的地址；$bSC_I$ 会原子地 在 $iSC_I$ 中确认 $GUID$ 的正确性。这一步将产生交易 $T_I^{redeem}$。$lock\_I(nft\_id\_on\_I,\ GID,\ address\_on\_B) \rightarrow T_I^{redeem}$ 

(ii) ***Bridge Core 上解锁***。 *redeemer* 将 $T_I^{redeem}$ 提交至 $vSC_I$ 并在 chain relay 中验证通过后，会在 $vSC_I$ 中：

- 记录 $GID$ 和 $nft_I^{x', n'}$ 的对应关系，
- 判断目的地公链并触发相应的 $vSC_B$ ,

在 $vSC_B$ 中, 

- 通过 $GID$ 检索，销毁 $nft_{BC}^{I,n'}$ ，产生 $nft_{BC}^{B, n}$ ，$ redeem\_ex(\ GID,\ nft\_id\_on\_B,\ address\_on\_I) \rightarrow EX_{issue}$

以上过程均在一次 Extrinsic 内触发，将会产生一笔 Extrinsic id，记录为 $EX_{redeem}$

(iii) ***解锁***。 *redeemer* 将 $EX_{redeem}$ 提交给 chain $B$ ， 经过 $iSC_B$ 验证通过后，在 $iSC_B$ 中会记录 $GUID$ 和 $nft_B^{x,n}$ 的对应关系， 同时会原子地触发 $bSC_B$ 中的方法，将 $nft_B^{x,n}$ 还给指定的地址。 

<img src="https://tva1.sinaimg.cn/large/006y8mN6gy1g7szni9t0lj30r70elgn2.jpg" alt="chain-relay-framework-2" style="zoom:50%;" />

### D. Algorithms 

##### Protocol: Issue

<img src="https://tva1.sinaimg.cn/large/006y8mN6gy1g7sznio88rj30uc0j0aca.jpg" alt="image-20191010113808729" style="zoom:50%;" />

解释：

###### *requester* 相关的操作：

- **lockB**($nft_B^{x,n}$, cond):   发生在 chain $B$ 内。将 $nft_B^{x,n}$ 锁定在 $bSC_B$ 中，并声明 *requester* 在 chain $I$ 上的地址，这个操作对应交易 $T_B^{lock}$

- **verifyBOp**(lockB, $T_B^{lock}$, $\Delta_{lock}$) $\rightarrow T$ :    发生在 Bridge Core 内。*requester*将 $T_B^{lock}$ 提交至 Bridge Core 中的 $vSC_B$ 进行验证，如果 $T_B^{lock}$ 真实地在 chain $B$ 上发生过并且满足最小需要延时 $\Delta_{lock}$，即返回结果 T(True)，否则返回 F(False).  

  如果结果为 T，则在 $vSC_B$ 中自动触发 newGid($nft_B^{x,n}$)，会产生新的 GID，以及 $nft_B^{x,n}$ 在 Bridge Core 内的镜像 $nft_{BC}^{B,n}$ ，并建立 GID 和 $nft_{BC}^{B,n}$ 的对应关系

- **verifyBCOp**(trigger, $EX_{issue}$, $\Delta_{trigger}$) $\rightarrow T$ :  发生在 chain $I$ 内。*requester* 将 $EX_{issue}$ 提交至 chain $I$ 的 $iSC_I$ 内，如果 $iSC_I$ 验证 $EX_{issue}$ 的真实性即返回 T，否则返回 F。验证通过后，即通过调用 issue 方法，发行 $nft_I^{x',n'}$ 到 *requester* 在 chain $I$ 的地址上。

###### *validator* 相关操作：

- **issueTransform**($vSC_I,\ pk_I^{requester},\ GID$ ): *validator* 会自动触发 $vSC_I$ 中的方法， 将 $nft_{BC}^{B,n}$ 销毁并产生 $nft_{BC}^{I,?}$ 表示在 chain $I$ 上即将新增发的 nft 的镜像（这里之所以用 $?$ 因为此时 chain $I$ 上的 nft 还未被增发，因此无法获取其 token id），并建立 GID 和 $nft_{BC}^{I,?}$ 对应关系。这次操作将产生 $EX_{issue}$.



##### Protocol: Transfer

![image-20190927191635665](https://tva1.sinaimg.cn/large/006y8mN6ly1g7fe8pswk9j3120050aac.jpg)

解释：

在 chain $I$ 上 *sender* 调用 $iSC_I$ 中的 transfer 方法，将 $nft_I^{x',n'}$ 发送给 *receiver*



##### Protocol: Redeem

<img src="https://tva1.sinaimg.cn/large/006y8mN6gy1g7sznhfdu6j314w0q0n0c.jpg" alt="image-20191010114326817" style="zoom:50%;" />

解释：

###### *redeemer* 相关操作：

- **burn**( $nft_I^{x',n'}$, $GID$, $pk_B^{redeemer}$ ) : 发生在 chain $I$ 上。 *redeemer* 触发 $bSC_I$ 的方法，将 $nft_I^{x',n'}$ 销毁，但保留销毁记录，$bSC_I$ 可以原子地检验 $GID$ 和 $nft_I^{x',n'}$ 对应关系。该操作将会产生交易 $T_I^{redeem}$ 
- **verifyIOp**(burn,  $T_I^{redeem}$,  $\Delta_{redeem}$) : 发生在 Bridge Core 内。用户将 $T_I^{redeem}$ 提交至 $vSC_I$ 中，如果 $T_I^{redeem}$ 真实地在 chain $I$ 上发生过并且满足最小需要延时 $\Delta_{redeem}$，即会根据 $GID$ 找到 对应的 $nft_{BC}^{I,?}$ 并根据自动补全成 $nft_{BC}^{I,n'}$ 
- **verifyBCOp**(trigger, $EX_{redeem}$, $\Delta_{trigger}$) $\rightarrow T$ :  发生在 chain $B$ 内。*redeemer* 将 $EX_{redeem}$ 提交至 chain $B$ 的 $iSC_B$ 内，如果 $iSC_B$ 验证 $EX_{redeem}$ 的真实性即返回 T，否则返回 F。验证通过后，即通过调用 issue 方法，即释放 $nft_B^{x,n}$ 到 *redeemer* 在 chain $B$ 的地址上。

###### *validator* 相关操作：

- **burnTransform**($vSC_B,\ GID,\ nft_{BC}^{x,n},\ pk_B^{redeemer}$ ): *validator* 自动触发 $vSC_B$ 中的方法， 将 $nft_{BC}^{I,n'}$ 销毁同时产生 $nft_{BC}^{B,n}$, 表示在 chain $B$ 上即将释放的 nft 的镜像。这次操作将产生 $EX_{redeem}$.



## 

## IV. Cross-chain NFT Standards

跨链环境下，NFT 会出现在不同的区块链网络中，并且其可用状态可能不断变化，因此类似原来单链网络内的标准和方案(例如，Ethereum ERC20)，已经无法满足跨链 NFT 标准的需要。

跨链 NFT 标准面临的识别性和解析问题，需要新的解决方案和标准来解决。因此我们引入一个基于通证跨链证明的解析系统来解决通证跨链时的定位和解析需求，通过通证解析系统和域内唯一标识，我们可以存在与不同域的通证之间的关联关系映射起来，并标识他们之间的相同与不同。

### A. 设计范围

- 全局唯一标识和外部本地标识规范

  为了将不同标准的通证标识符进行规范化，以提供识别和解析方法，与现有的标准进行很好的协调和对接，并满足社区基础设施建设的标准需求。识别标识分为全局唯一标识和外部本地标识。

- NFT 解析系统

- NFT 所有权管理

- Inter-parachain NFT Transfers

  通过引入一个关联的 SPREE 模块来帮助在不同的平行链之间进行跨链转账。

### B. 标准方案

基于跨链 NFT 协议的设计和方案基础，我们设计并提议了一个跨链 NFT 的标准提案，详细设计放在了 [RFC-0013 Cross-chain NFT Standards](./rfc-0013-darwinia-cross-chain-nft-standards)。



## 参考

[1] https://bitcoin.org/bitcoin.pdf

[2] https://github.com/ethereum/wiki/wiki/White-Paper

[3] https://github.com/EOSIO/Documentation/blob/master/TechnicalWhitePaper.md

[4] https://eips.ethereum.org/EIPS/eip-20

[5] https://eips.ethereum.org/EIPS/eip-721

[6] https://en.bitcoin.it/wiki/Hashed_Timelock_Contracts

[7] https://eprint.iacr.org/2018/643.pdf

[8] https://opensea.io/

[9] https://vitalik.ca/general/2018/04/20/radical_markets.html

[10] https://github.com/ethereum/wiki/wiki/Light-client-protocol

[11] https://elixir-europe.org/platforms/interoperability

[12] https://github.com/AlphaWallet/TokenScript

[13] https://github.com/darwinia-network/rfcs/blob/v0.1.0/zh_CN/0005-interstella-asset-encoding.md

[14] https://onlinelibrary.wiley.com/doi/pdf/10.1087/20120404

[15] https://wiki.polkadot.network/en/latest/polkadot/learn/spree/

[16] https://en.wikipedia.org/wiki/Unique_identifier

[17] https://en.wikipedia.org/wiki/Identifiers.org

[18] https://schema.org/

[19] https://medium.com/drep-family/cross-chains-a-bridge-connecting-reputation-value-in-silo-b65729cb9cd9

[20] https://github.com/paritytech/parity-bridge

[21] https://vitalik.ca/general/2018/04/20/radical_markets.html

[22] https://talk.darwinia.network/topics/99

