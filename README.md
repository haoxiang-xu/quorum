# Quorum

> **quorum** — 议事有效所需的最少出席者；在分布式系统中，则是达成决议所需的最小节点集。

一套 agent team 的宪法、协作程序与角色法典。

多 agent 协作最昂贵的失败之一，是在真正开始工作前就预测复杂度、召集所有可能相关者并选择一整套流程。Quorum 采用相反的方式：从一个主 owner 开始，只在真实边界出现时串行交棒，只在真实分歧无法吸收时开庭。

## 文档

| 文件 | 内容 |
|---|---|
| [`docs/quorum/README.md`](docs/quorum/README.md) | Quorum 规范文档目录与推荐阅读顺序 |

## 核心设计

**人是唯一的裁决者。** `Chief Judge` 由人担任。owner 共识、异议人数与程序投票都不能替代人的实体裁定。

**议案和方案不是步骤。** 议案判断一件事是否成立、正确、必要、合规或有影响；方案说明具体怎么做。议案可以延伸议案，方案可以延伸方案。需要 action 的议案结论只会产生一个新的方案 case，不会自动进入“下一阶段”。

**所有事项从一个 owner 开始。** Speaker 不预测完整 roster，只选择一个最接近核心问题的主 owner。主 owner 像独立工作一样先完成自己的部分，不能负责的部分留空并说明如何交给下一位 owner。

**跨 owner 通过串行交棒发现。** Speaker 一次只把一个有限问题或方案块交给下一位 owner。每位 owner 只补全自己的边界，最终返回主 owner 集成。

**异议而不是风险标签触发程序。** 主 owner 接受异议时直接修改；拒绝一项实质异议时进入 Debate 辩论庭。多个相同或可共同处理的异议仍合并为一次聚焦辩论。

**Full 叫众议庭。** 只有至少三名且严格超过半数的合作 owner 形成无法合并的被拒异议，Speaker 才能发起程序投票；严格过半支持后才升级为 Full（众议庭）。众议庭全面审理当前议案或方案，但不把二者串成步骤。

**方案先于 action。** 任何真实 action 都必须由 Chief Judge 批准一份带验收标准的方案。默认协作可以很轻，但方案本身不能缺失。

**接缝是一等方案对象。** 跨 owner、进程、provider、持久化或版本边界的义务写成 `BC-###`；依赖历史状态的行为写成 `SEQ-###`。两者进入方案、交棒、审查与验收，不靠某个 agent 恰好记得。

**相关性先于完整性。** Speaker 只保留会改变当前结论、方案、owner 分工、验收、回滚或裁定的内容。真实但无关、重复或范围外的材料不会扩大参与名单或庭审。

**证据程序只在需要时启动。** 默认协作不制造 BOS、DES、Examiner 或空报告；正式庭审、Chief 明示的裁定前核验或 material 验收证据才冻结最小决策证据集，并在适用时执行一次 16% 随机抽查。

**分歧是产出。** 辩论不以全员一致为目标。没有实质新增量时，Speaker 把稳定分歧原样交给 Chief Judge 裁定。

## 状态

设计阶段。规范采用“议案/方案 × 协作/辩论庭/众议庭”的正交模型，运行时与 department 尚未落地；仓库提供无外部依赖的 reference conformance linter，但它不构成运行时或新的规范来源。

## Reference conformance linter

`tools/quorum_lint` 对 canonical `case.md / proposal.md / record.md / ruling.md / acceptance.md` 做 boundary protocol v1 的结构与引用检查。它不生成持久 manifest，不验证业务事实，也不能替代真实测试证据。

```bash
python3 -B -m tools.quorum_lint path/to/case --phase ruling
python3 -B -m tools.quorum_lint path/to/case --phase acceptance
python3 -B -m unittest discover -s tests -v
```

`ruling` 从 `case.md` frontmatter 读取 canonical protocol，并交叉检查 latest PS、contract set、BC/SEQ refs；随后检查 ID/key/field 唯一性、精确同案 refs、公共事件信封、applicability、`contract_set`/stateful 声明、AC 引用、material 串行 HS 与全部合法 terminal、64-hex SHA-256 revision pair、PS boundary/content hash、canonical RS electorate/N/deadlines/content hash、owner stance/显式 successor inheritance、有限 objection 与 disposition 路由。若存在 ACTION ruling，还重算 `PENDING_CLOSURE` bundle/commit hashes并要求唯一 canonical commit。`acceptance` 只从已经 closure commit 生效的 latest `PLAN_RULING` 读取 effective PS/AC/expected revisions，只读取当前最新且时间在 commit 后的 AT，并要求 actual revisions 有外部证据、每个获准 AC 有具体方法、绑定该 AC 的 canonical E 和 `PASS`，同时核对 case status 因果。linter 不决定某个未标记 case 是否早于采用方的 effective-from，也不会自动豁免 legacy；调用方必须在进入本 v1 blocking gate 前以自己的冻结 effective-from/legacy 清单完成路由，非 v1 输入会失败。linter 只验证记录的结构与精确值关系，不证明 owner 判断、revision 内容或测试结论真实。
