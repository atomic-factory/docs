---
id: wiki-tut-identiey
title: 如何设置链上身份
sidebar_label: 设置链上身份
custom_edit_url: https://github.com/darwinia-network/docs/edit/master/content/zh-CN/wiki-tut-identity.md
---

## 什么是身份注册商？
身份注册商的主要工作就是验证链上身份真实性，并根据身份信息的完整性或真实性，授予不同的身份等级。获得一个较高的身份等级，会增加信任度与社区知名度，有利于参与链上治理、竞选验证人等活动。


## 如何查找当前身份注册商？
- 方式 1: APPS 网页钱包
您可以在 Chain state> Storage> Identity> registrars: Vec<Option<RegistrarInfo>> 中查看所有身份注册商信息，包括注册商账号、注册费、权限等。
![dev_identity_1](assets/dev_identity_1.png)
> 注册费有可能发生变动，申请身份注册前建议查询当前费用，选择合适的注册商。

- 方式 2:Subscan
通过 Subscan 浏览器，账户 > 身份筛选，也可以查看该网络的所有身份注册商。
![dev_identity_2](assets/dev_identity_2.png)


## 如何获得身份认证（加 V）？

- 1、设置链上身份
点击切换账号 > 设置 > 设置链上身份
![dev_identity_3](assets/dev_identity_3.png)
填写链上信息，请确认信息的真实性与格式（格式参考下图），确认无误后点击「设置」。
![dev_identity_4](assets/dev_identity_4.png)
> 请谨慎填写身份信息，身份信息通过验证（加 v）后，更改身份信息需要重新验证身份。

- 2、申请验证
点击左侧列表中的「交易」>  选择交易类型： identity / requestJudgement > 设置注册商参数：reg_index 和 max_fee，确认无误后点击「签名并发送」。
下图以#0 注册商 subscan 为例，reg_index 填写 0，max_fee 填写 100。查找注册商参数的方法请参考上文“如何查找当前身份注册商？”
![dev_identity_5](assets/dev_identity_5.png)

- 3、联系注册商完成验证
确认交易发送成功后，请通过注册商留下的身份信息联系注册商，完成验证。每个注册商的验证规则可能都不同，具体规则请联系验证商获取。
![dev_identity_6](assets/dev_identity_6.png)

- 4、验证完成
全部验证通过后，你的账号将获得身份标识，在验证人中更加瞩目。
![dev_identity_7](assets/dev_identity_7.png)


## 身份等级

- 一般：数据看似合理，但未进行深入检查，例如正式的 KYC 流程。
- 很好：注册商已证明信息正确无误。
- 未知：默认值，尚未做出验证。
- 过时的：该信息曾经很好，但是现在已经过时了。
- 质量低劣：信息质量低劣或不精确，但可以通过更新进行修复。
- 错误：信息错误，可能表示恶意。
