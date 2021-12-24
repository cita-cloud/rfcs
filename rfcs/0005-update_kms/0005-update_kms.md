---

---

| Number | Category     | Status | Author | Organization | Created    |
| ------ | ------------ | ------ | ------ | ------------ | ---------- |
| 0005   | Modification | Draft  | 胡江 | 溪塔科技     | 2021-12-24 |

# 004-CITA_CLOUD_PROTO Kms

# 概述

增加密钥生命周期管理能力，降低Kms微服务与cita_cloud中其他微服务之间的耦合，使之更为通用

# 动机

Kms作为单独的服务在实际使用中，提供的能力不完善，且某些功能与其他微服务存在耦合性

# 设计方案
- 增加密钥生命周期管理能力
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
    
    - 预删除
    ```
    message ScheduleDeleteKeyRequest {
        uint64 key_id = 1;
        uint64 pending_days = 2;
    }
    ```
    
    ```
    rpc ScheduleDeleteKey(ScheduleDeleteKeyRequest) returns (EmptyDataResponse);
    ```
    增加预删除接口。其中pending_days为密钥预删除周期，取值范围：7~30。 单位：天。
    
    - 撤销删除
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
增加密钥导入接口
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
rpc ImportKey(ChangeKeyStatusRequest) returns (ImportKeyResponse);
```
其中encryptedKey为base64编码后的密钥材料。

建议删除接口
```
rpc CheckTransactions(blockchain.RawTransactions) returns (common.StatusCode);
```

该接口与cita_cloud其他微服务存在耦合性，建议改用其他方式实现此功能

详细代码更新见 [cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto)
# 替代方案

无

# 兼容性

可以兼容。

# 参考实现
 
[cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto) 版本

