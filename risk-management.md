# 风险管理

## 我们追求的风险管理实践

* 有明确的流程处理线上事故，团队全员参与，而不是仅有Dev。
* 聚焦于解决问题而不是追责。
* 有明确的事故评级方案，并按照综合评级安排修复计划，而不是一味紧急抢修。
* Dev有权拒绝为低优先级的事故加班。
* 严重事故处理之后有 Retro，产出的 "Lesson Learned" 需要整理成文档分享给其他团队。
* 定期更新威胁建模，并积极执行产出的 Actions。

[comment]: <> (TODO)

### 为什么要有风险管理

[comment]: <> (TODO)

### 怎么做才算风险管理
* 线上事故处理流程（Draft）
  - Downtime 小于30分钟：
    事后发送处理报告和时间线给 Stakeholder 和受到影响的用户。
  - Downtime 大于等于30分钟：
    立即发送 Outage 信息给用户，并告知当前的处理进度和预计恢复的时间。
    事后处理同上。
* 线上事故评级方案（Draft）

  |维度/优先级|高|中|低|
  |----|----|----|----|
  |影响的功能|核心功能|特定功能|边缘功能|
  |影响的范围|核心用户群或所有用户|特定用户|极少数用户|
  |影响的后果|敏感数据被破坏或泄露|普通数据被破坏或泄露|边缘数据被破坏或泄露|
  |复现成功率|重复特定步骤可100%复现|特定条件复现（如时间地点）|复现条件极难达成|
  |复现复杂度|业务流程中的正常操作|普通用户的异常操作|有技术背景的用户的异常操作|