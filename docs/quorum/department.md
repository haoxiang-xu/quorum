# Department 部门

[Quorum 索引](README.md)

department 是对 agent 进行分类管理的 **组织单位**，服务于 `Chief Judge` 对 agent 的归类和管理，不是庭审流程中的实体。

- 组织规则:
    - 一个 department 对应一个 **folder**，folder 名即 department 名
    - 每个 agent 必须属于 且只属于 一个 department
    - department 可以拥有自己的 **agents**，也可以拥有自己的 **skills**
    - department 的 创建、合并、拆分、删除属于持久组织变更，必须由 proposal 说明 agent/skill 迁移、回滚与验收，并经 `Chief Judge` 批准；纯议案只能判断是否应变更，不能直接执行

- skill 规则:
    - department 的 skills，对该 department 内的所有 agent 可用
    - agent 只感知 skill 本身，不感知 skill 来源的 department 层级

- 透明性原则:
    - department 仅用于分类管理，agent 不需要 aware 自己所属的 department
    - department 不进入庭审: 庭审中 agent 以 **角色身份** 发言，不存在 department 立场
    - department 的划分与调整，不改变 agent 的 命名规则，记忆，和职责范围

- folder 结构示例:

```
departments/
├── court/                          # 庭审程序类角色
│   └── agents/
│       ├── speaker-of-the-house
│       ├── procedural-judge
│       ├── evidence-examiner
│       ├── acceptance-inspector
│       └── codex
├── llm-agent/                      # 按代码库划分的 department
│   ├── agents/
│   │   ├── code-owner-llm-agent
│   │   └── code-owner-llm-agent-memory
│   └── skills/
├── expertise/                      # 按专业领域划分的 department
│   ├── agents/
│   │   ├── expert-architecture
│   │   ├── expert-security
│   │   └── expert-llm
│   └── skills/
└── product/                        # 按业务领域划分的 department
    ├── agents/
    │   ├── task-owner-user-interview
    │   └── knowledge-owner-market-research
    └── skills/
```
