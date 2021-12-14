---

---

| Number | Category     | Status | Author | Organization | Created    |
| ------ | ------------ | ------ | ------ | ------------ | ---------- |
| 0004   | Modification | Draft  | 姚鹏飞 | 溪塔科技     | 2021-09-04 |

# 004-CITA_CLOUD_PROTO StatusCode

# 概述

StatusCode 为微服务 `grpc` 通信提供了错误处理的能力。

# 动机

cita_cloud_proto v6.2.0 的版本对于 `grpc` 调用过程中，微服务在执行过程中产生的错误传递给对方微服务后，对方微服务无法识别也无法进行相应的错误处理。

# 设计方案

将微服务间会需要传递的错误信息记录在 [status_code](https://github.com/cita-cloud/status_code) 仓库中，并提供 status_code 与 u32 的转化能力。同时 cita_cloud_proto 中微服务之间的 `grpc` 增加 u32 的封装，微服务将可以根据该字段获取错误信息，需要进行修改的接口如下：
``` proto
rpc Reconfigure(common.ConsensusConfiguration) returns (common.SimpleResponse) ->
rpc Reconfigure(common.ConsensusConfiguration) returns (common.StatusCode)

rpc CheckBlock(common.ProposalWithProof) returns (common.SimpleResponse) ->
rpc CheckBlock(common.ProposalWithProof) returns (common.StatusCode)
 
rpc CheckProposal(common.Proposal) returns (common.SimpleResponse) ->
rpc CheckProposal(common.Proposal) returns (common.StatusCode)
 
rpc CommitBlock(common.ProposalWithProof) returns (common.ConsensusConfiguration) ->
rpc CommitBlock(common.ProposalWithProof) returns (common.ConsensusConfigurationRespond)

rpc VerifyDataHash(VerifyDataHashRequest) returns (common.SimpleResponse) ->
rpc VerifyDataHash(VerifyDataHashRequest) returns (common.StatusCode)

rpc SendMsg(NetworkMsg) returns (common.SimpleResponse) ->
rpc SendMsg(NetworkMsg) returns (common.StatusCode)

rpc Broadcast(NetworkMsg) returns (common.SimpleResponse) ->
rpc Broadcast(NetworkMsg) returns (common.StatusCode)

rpc RegisterNetworkMsgHandler(RegisterInfo) returns (common.SimpleResponse) ->
rpc RegisterNetworkMsgHandler(RegisterInfo) returns (common.StatusCode)

rpc ProcessNetworkMsg(NetworkMsg) returns (common.SimpleResponse) ->
rpc ProcessNetworkMsg(NetworkMsg) returns (common.StatusCode)

rpc Store(Content) returns (common.SimpleResponse) ->
rpc Store(Content) returns (common.StatusCode)

rpc Delete(ExtKey) returns (common.SimpleResponse) ->
rpc Delete(ExtKey) returns (common.StatusCode)
```

# 替代方案

将 status_code的所有错误枚举全部写入 cita_cloud_proto 中，将错误信息纳入到协议中。

# 兼容性

可以兼容。

# 参考实现

已上传了一个 [status_code](https://github.com/cita-cloud/status_code) 版本

