---

---

| Number | Category      | Status | Author | Organization | Created    |
| ------ | ------------- | ------ | ------ | ------------ | ---------- |
| 0003   | Informational | Draft  | 姚鹏飞 | 溪塔科技     | 2021-08-13 |

# CITA-CLOUD Release Flow

## 前言

> CITA-CLOUD 的发版原则与过往的发版方式完全不同，他不是一种完全连续、线性的增长方式，但其发版规则也有一套不断在完善的发版策略，~~这套策略我们认为也是最适合 CITA-CLOUD 的云原生的发版策略~~。CITA-CLOUD 将以 [cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto) 为主线进行版本更新，其 release notes 会放在 [operator](https://github.com/cita-cloud/operator/releases) 下面。

## 发版原则

发版大版本为 X.0.0，发版小版本为 \*.X.0，尽量以发小版本为主，避免发大版本；并且发大版本遵守最新依赖原则，也就是每一次发 X.0.0 需要对 `Cargo.toml` 的依赖进行一次更新，推荐使用工具 [cargo-outdated](https://github.com/kbknapp/cargo-outdated)；微服务版本与 [cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto) 对齐（微服务可能出现版本跳跃的情况:rocket:，微服务的版本并非**线性**）。

## 详细规则

### 微服务发版

目前各个微服务的发版依照分支发版原则，假设将要发版本为 N.M.0，那么发版操作为提交新分支 `vN.M.0` 即为发版操作，同时该分支原则上提交分支即为封版，同时准备微服务的 release notes。[operator](https://github.com/cita-cloud/operator/releases) 再对各个微服务的 release notes 进行整合以便 CITA-CLOUD 发版。

### [cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto)

假设当前版本为 N.M.0，现在要做一项改动，并将改动之后的版本暂名为 TBD，会存在两种情况：1. 节点兼容个别几个微服务使用 TBD 同时其他微服务可以采用 N.\*.0（大版本相同的向下兼容），并且该节点可以与其他使用 N.\*.0 节点组网的情况下，TBD 为 N.M+1.0；否则 TBD 为 N+1.0.0。当然也可以人为的定义发大版本，大版本并非 **hardfork**。

### N+1.0.0 大版本更新

1. N+1.0.0 意为大版本更新，同时其他希望继续 working 的微服务都需要更新到 N+1.0.0；
2. 大版本更新时，各个微服务的依赖 `Cargo.toml` 原则上也更新到最新版本，`Cargo.toml` 中更新依赖的版本号；
3. 同一个大版本（ N+1.*.0）期间，不需要进行依赖的集体更新。

### N.M+1.0 小版本更新

1. N.M+1.0 意为小版本更新，小版本的更新意图是在大版本不变的情况下，小版本（N.[0-M].0）向下兼容情况下的更新；
2. 微服务小版本的更新需要与 `cita_cloud_proto` 保持一致，也就是以来的 `cita_cloud_proto` 版本与微服务版本一致

```rust
[package]
name = "micro-service"
version = "N.M.0"

[dependencies]
cita_cloud_proto = { git = "https://github.com/cita-cloud/cita_cloud_proto", branch = "N.M.0" }
```

3. 微服务的版本更新建议使用最新的 `cita_cloud_proto`，例如当前的微服务版本为 N.M.0，`cita_cloud_proto` 版本为 N.M+2.0，那么微服务版本和 `cita_cloud_proto` 的依赖的更新到 N.M+2.0，有利于起链时版本的选择

## 未解决的问题

1. 假设某个微服务的功能更新不需要修改 `cita_cloud_proto`，那样版本号如何定义：N.M.X -> N.M.X+1?
2. 对于1，如果采用 N.M.X+1 `operator` 这边又如何发版呢？
3. 修改1，改为 `cita_cloud_proto` bump to N.M.X -> N.M+1.X?
4. 对于3，其他微服务后续版本更新如何操作？需要改proto的和不需要改proto的各如何操作？
5. 如果 `cita_cloud_proto` 长时间不需要做大版本更新，甚至无代码更新，需要引入时间的大版本更新机制？

6. 可能时间发版才是最简单的方式？

## 总结

目前该方案还有诸多问题未解决，尚不足以作为最终方案。