---

---

| Number | Category     | Status | Author | Organization | Created    |
| ------ | ------------ | ------ | ------ | ------------ | ---------- |
| 0005   | Modification | Draft  | 胡江 | 溪塔科技     | 2021-12-24 |

# 004-CITA_CLOUD_PROTO Kms

# 概述

增加密钥生命周期管理能力，降低`Kms`微服务与`cita_cloud`中其他微服务之间的耦合，使之更为通用

# 动机

`Kms`作为单独的服务在实际使用中，提供的能力不完善，且某些功能与其他微服务存在耦合性。
商用`Kms`服务包含密钥服务，凭据管家和证书管家三部分。
- 密钥服务  
  提供密钥的全托管和保护能力
- 凭据管家  
  提供凭据（数据库账号口令、服务器账号口令、SSH Key、访问密钥等凭据）的全生命周期管理和安全便捷的应用接入方式，帮助您规避在代码中硬编码凭据带来的敏感信息泄露风险。
- 证书管家  
  提供高可用、高安全的密钥和证书托管能力，以及签名验签能力。  

`CITA_CLOUD`的`Kms`服务提供密钥托管，数据加密/解密，签名/验签功能，与商用`Kms`的密钥服务功能吻合，新增接口设计参照商用`Kms`的密钥服务。

# 设计方案
- 增加密钥生命周期管理能力  
  商用`Kms`服务对于密钥生命周期的管理，包括密钥的创建，禁用，删除，轮转，别名及导入功能。
  密钥轮转通过添加版本的方式定期更换密钥，从而加强密钥使用的安全性。而`CITA_CLOUD`的`Kms`服务的密钥与节点的账户具有绑定关系，无法进行轮转。
  别名是对不同地域下密钥管理的标识，`CITA_CLOUD`的`Kms`无需使用别名进行密钥管理。
  - 开启/关闭
    ```
    message ChangeKeyStatusRequest {
        uint64 key_id = 1;
        bool enable = 2;
    }
    ```
    ```
    rpc ChangeKeyStatus(ChangeKeyStatusRequest) returns (EmptyDataResponse);
    ```
    增加更改密钥状态接口。只有处于开启状态的密钥可以加密/解密，签名/验签  
  - 删除  
    商用Kms服务密钥删除功能包含开启/关闭删除保护，计划删除/取消计划删除
    - 删除保护  
      `enable`为0，关闭保护，为1开启保护
      ```
      message SetDelProtectionRequest {
          uint64 key_id = 1;
          bool enable = 2;
      }
      rpc SetDeletionProtection(SetDelProtectionRequest) returns (EmptyDataResponse);
      ```
      增加删除保护接口。为密钥开启删除保护后，将无法通过API删除该密钥，从而避免误删除密钥。
    - 预删除/撤销删除  
      用户主密钥一旦删除，将无法恢复，使用该CMK加密的内容及产生的数据密钥也将无法解密。因此，对于用户主密钥的删除，KMS只提供申请删除的方式，而不提供直接删除的方式。如果需要删除用户主密钥，推荐您使用禁用密钥功能。
      
      ```
      message ScheduleDeleteKeyRequest {
          uint64 key_id = 1;
          uint64 pending_days = 2;
      }
      ```
      
      ```
      rpc ScheduleDeleteKey(ScheduleDeleteKeyRequest) returns (EmptyDataResponse);
      ```
      增加预删除接口。其中`pending_days`为密钥预删除周期，取值范围：`7~30`。 单位：天。

      ```
      message CancelDeleteKeyRequest {
          uint64 key_id = 1;
      }
      ```

      ```
      rpc CancelDeleteKey(CancelDeleteKeyRequest) returns (EmptyDataResponse);
      ```
      增加撤销删除接口。撤销删除后，密钥为启用状态。
      

- 密钥导入  
  当创建密钥材料来源为外部的密钥时，`Kms`不会为创建的用户主密钥生成密钥材料，此时您可以将自己的密钥材料导入。
  ```
  message ImportKeyRequest {
      bytes encryptedKey = 1;
  }
  message ImportKeyResponse {
      common.StatusCode status = 1;
      GenerateKeyPairResponse data = 2;
  }
  message GenerateKeyPairResponse {
      uint64 key_id = 1;
      bytes address = 2;
  }
  ```
  ```
  rpc ImportKey(ImportKeyRequest) returns (ImportKeyResponse);
  ```
  增加密钥导入接口。其中`encryptedKey`为`base64`编码后的密钥材料。

建议修改接口
```
rpc CheckTransactions(blockchain.RawTransactions) returns (common.StatusCode);
```

该接口与`cita_cloud`其他微服务存在耦合性，建议改用其他方式实现此功能

详细代码更新见 [cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto)
# 替代方案

无

# 兼容性

可以兼容。

# 参考实现
 
[cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto) 版本

