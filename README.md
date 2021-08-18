# CITA-Cloud RFCs

`CITA-Cloud` 最重要的是微服务划分以及之间的协议（[cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto)），各个微服务的实现只需要遵循协议，实现对应微服务的接口即可。

因此，各个微服务实现的修改按照普通的 `GitHub PR` 流程即可。

但是，协议相关的修改，一定要经过充分的讨论和论证，并由整个开发团队达成一致结果。

`RFC` 处理流程就是为了保证协议的修改有一个一致的、可控的路径，以便开发团队以及相关开发者能够对 `CITA-Cloud` 的发展方向有清晰的认识。

## 准备工作

在大家都不了解你的想法的情况下，匆忙提出的 `RFC` 可能会受到大家的冷落。不符合 `CITA-Cloud` 发展路线的 `RFC` 可能最终会被舍弃，导致前功尽弃。

因此，最好是先在一些非正式场合抛出你的想法，与开发者一起讨论，确认想法是可行的。比如在 [CITA-Cloud 论坛](https://talk.citahub.com/c/24-category/24) 发帖子，或者在本仓库的 [issues](https://github.com/cita-cloud/rfcs/issues) 中提 `issue`。

## 处理过程

`RFC` 格式为 `markdown` 文本文件，具体格式可以参见 `rfcs` 目录下的 `rfc-template.md`。

1. `fork` 当前仓库。
2. 参考模板文件，创建具体的 `rfc` 文件并补充相关内容。
   
   2.1. 编号为四位自增的数字，自行在当前最新的编号后加一。

   2.2. 每个 `rfc` 一个文件夹，以方便存放插图等附件，文件夹名字与 `rfc` 文件名字保持一致。

   2.3 分类有 `Informational` 和 `Protocol`。后者为微服务协议的变更，前者为一般的信息性内容，比如白皮书，发展路线等。

   2.4 状态初始为 `Draft`，经过讨论修改变为 `Accepted`。如果后续被废弃，则变为 `Obsolete`。如果涉及代码实现，在实现完成后变更为 `Implemented`。
3. 提交 `PR`。社区成员在 `PR` 中进行讨论，作者根据反馈进行内容修订。
4. 如果 `rfc` 得到开发者的一致认同，或者在半数以上开发者发表过意见之后的两周内没有新的建议，则表示接受。
5. 如果 `rfc` 提出两周之后，没有任何开发者参与讨论，则自动关闭 `PR`，表示拒绝。

## 参与角色

* `rfc` 作者。发起编写 `rfc` 并积极推动其得到接受和实施的一个或者多个社区成员。
* `rfc` 编辑。负责审查并判断是否采纳 `rfc` 的一个责任人或者一个委员会。目前社区人员比较少，暂定为包含所有核心开发者的团队。
* 参与者。参与讨论并给出各种的社区成员。

## 实施

协议类型的 `rfc` 在接受之后通常需要修改 [cita_cloud_proto](https://github.com/cita-cloud/cita_cloud_proto)。

相关的代码修改按照普通的 `GitHub PR` 流程进行，但是要在其中关联已经接受的 `rfc` 的相关链接。

检视人员要确保其代码实现与已经接受的 `rfc` 中的方案描述是一致的。

`cita_cloud_proto` 代码修改合并之后，还要修改对应的微服务的实现。直到所有仍在维护中的微服务完成代码更新，才会在之后的版本中正式发布该特性，所以 `rfc` 的实施会是一个比较漫长的过程。

其中有些 `rfc` 可能会因为依赖其他 `rfc`，或者有更重要的 `rfc` 需要实施，排期会往后延。

`rfc` 的作者不一定要参与后续的实施环节，但是非常欢迎作者参与相关 `PR` 的代码检视。


## 参与贡献

欢迎大家参与跟 `CITA-Cloud` 有关的任何方面的讨论，并给出您宝贵的建议。

地点可以是[CITA-Cloud 论坛](https://talk.citahub.com/c/24-category/24)，亦或本组织下任何仓库的 `issues`区和 `PR`区。
