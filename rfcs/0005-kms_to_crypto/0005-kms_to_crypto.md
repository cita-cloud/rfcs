---

---

| Number | Category     | Status | Author | Organization | Created    |
| ------ | ------------ | ------ | ------ | ------------ | ---------- |
| 0005   |   Protocol   |Accepted| 宁志伟 |  溪塔科技     | 2022-02-07 |

# 0005-kms_to_crypto

# 概述

`kms` 微服务在设计之初就是为解耦，将链上密码学算法相关的操作剥离到一个独立的微服务。

微服务接口设计时参考了`密码机`的功能，类似于[PKCS#11](https://docs.oasis-open.org/pkcs11/pkcs11-base/v2.40/os/pkcs11-base-v2.40-os.html)的简化版。

其中除了提供签名算法和哈希算法相关的接口外，还提供了简单的密钥管理功能，可以创建并管理多个密钥。

后面实际使用中，密钥管理功能过于简单，因此没有实际使用，几乎每个微服务实例中都只有一个密钥。

但是密钥管理功能却导致`kms`微服务成为一个有状态，需要持久化存储的微服务，非常不利于整个系统的配置，扩展，迁移等操作。

本提案拟将`kms`微服务更名为`crypto`，恢复其本身的设计初衷，避免给开发者和用户造成困扰。同时，剔除微服务中密钥管理相关的功能，由单独的成熟应用或者设施来提供密钥管理功能。

# 动机

简化`kms`微服务的职能，使得其更加专一，更加简单。

额外的密钥管理功能由更加专业，更加成熟的系统来提供。

简化整个系统的配置，扩展和迁移等操作的复杂度。

# 设计方案

去除原有的生成密钥对的接口。

```
message GenerateKeyPairRequest {
    string Description = 1;
}

message GenerateKeyPairResponse {
    uint64 key_id = 1;
    bytes address = 2;
}

rpc GenerateKeyPair(GenerateKeyPairRequest) returns (GenerateKeyPairResponse);
```

密钥直接通过独立配置文件的方式配置给微服务。

在`k8s`环境，可以通过`secret`的方式来配置密钥，保证不会被随意泄漏。

如果有更高的安全性要求，还可以通过[kms-provider](https://kubernetes.io/zh/docs/tasks/administer-cluster/kms-provider/)对`secret`的内容进行加密。

同时，`SignMessage`接口的参数

```
message SignMessageRequest {
    uint64 key_id = 1;
    bytes msg = 2;
}
```

也无需`key_id`项了。


# 替代方案

之前也考虑过完善`kms`微服务中密钥管理部分的功能。

将其作为一个完整的`KMS`服务，独立于节点之外的一个公共服务。

但是经过调研，这部分功能过于复杂，需要非常多的专业知识。

因此，还是走简化方案，专注于为`CITA-Cloud`提供密码学服务。

# 兼容性

### 配置工具

目前只有配置工具会调用`GenerateKeyPair`接口，改为直接创建账户即可。

### 微服务修改

`Controller`和`Consensus`微服务会调用`SignMessage`接口，但是只需要修改一下传入的参数即可，修改量不大。

# 参考实现

无

