# Skill 优化工作流

> 本文件为 `solution-expert` 的 **Skill 优化**处理模块。按需加载，仅在入口分流命中"Skill 优化"路径时由主 Agent Read。

---

## Skill 优化前置（Step 1 入口）

### 命中条件

用户要求"对 X skill 出优化方案" / "X skill 加新功能" / "X skill 文档太乱"

### 不适用

用户问"X skill 怎么用" → 不进入本前置，走 §评审 路径或 §退出

### 触发动作

按需 `Read references/skill-optimization-rules.md` 拿到约束知识库

---

## 四档分类与触发策略

调研阶段先对**目标 skill**做四档分类，再决定后续 §1-§4 的侧重：

| 分类 | 示例 | 触发策略 |
|------|------|---------|
| 自身 skill（solution-expert） | 用户说"solution-expert 需要新功能" | ✅ 必读 `references/maintenance.md` 修订记录 + 走 §修改本 skill 的硬性约定 4 件事 |
| 同源 skill（skill-iter） | 用户说"skill-iter 加个并发轮次" | ✅ 必读 skill-iter SKILL.md 头部声明 + 走其自身 §修改本 skill 的硬性约定 |
| 第三方 skill（code-review / refactor / test / security-review / simplify / deep-research） | 用户说"code-review 误报率太高" | ✅ 必读 §跨 skill 协作 表 + §跨 skill 联动粒度与去抖规则 |
| 未在册 skill | 用户给一个陌生 skill 路径 | 先 Read 该 skill 的 SKILL.md frontmatter 与最近 commit，再决策 |

---

## 错误处理

| 异常场景 | 行为 | 兜底提示 |
|---------|------|---------|
| 未在册 skill 的 `SKILL.md` Read 失败（路径不存在 / 权限拒绝） | 不进入"按需加载"前置，直接退回 §入口分流 原有 4 路径分支 | "目标 skill 路径不可读（{path}），请确认路径或选择现有场景路径" |
| `references/skill-optimization-rules.md` 文件缺失（被误删 / 未双处落盘） | 降级到 §Step 1 默认流程（不阻塞 Step 1-4），但在 §8 风险表 RISK-3 关联的监控项 `wc -l` 报警 | "知识库文件缺失，已降级到 §Step 1 默认流程；请补全 references/skill-optimization-rules.md" |
| 用户在 Step 2/3 中途切换目标 skill（path A → path B） | 终止当前 Step 2/3 调研证据台账，回到 §Step 1 重新执行分类判定 | "目标 skill 已切换，重新走 §Step 1 前置分类" |

---

## 调研证据台账追加

触发后，**必须**在调研证据台账新增 1 行：

| 关键结论 | 状态 | 证据 / 待确认来源 | 证据时间锚 | 对范围、选型或 DoD 的影响 |
|----------|------|------------------|------------|--------------------------|
| 目标 skill 路径 | 类型（自身/同源/第三方/未在册） | 命中约束 ID 列表 | — | 分类决策依据 |

---

## Step 2 · 澄清关键决策

与项目方案类似，但决策维度是 **skill 而非业务系统**：

- **需求编号化（必填）**：`R1 / R2 / ...`
  - R-约束-N（命中知识库第 N 条硬约束）
  - R-触点-N（落到 SKILL.md / references/ 的具体触点）
  - R-类型-N（自身/同源/第三方/未在册分类）

- **范围**：本次涉及 SKILL.md 的哪些章节？references/ 的哪些文件？哪些明确不动？
- **目标**：优化意图是什么？（可维护性 / 可触发性 / 文档清晰度 / 新增功能）
- **约束**：目标 skill 的"反例/不要做"清单、现有 frontmatter 约定

---

## Step 3 · 设计与对比

- 候选方案比较维度：frontmatter 结构、章节组织、references/ 演进条款、硬约定措辞
- **明确给出推荐方案与理由**
- 推荐方案必须包含：模块划分、章节调整建议、references/ 演进计划

---

## Step 4 · 三阶段流水线

同项目方案 Step 4（草稿 → 审核 → 优化 → 终稿），但有以下**差异点**：

### 差异 1：§4 风险必须覆盖

必须覆盖以下风险：
- **RISK-1**：分类误判导致走错"修改本 skill 的硬性约定"
- **RISK-2**：references/ 文件被强行覆盖（违反 §1 编辑源原则）
- **RISK-3**：知识库膨胀变成"小 SKILL.md"

### 差异 2：§5 回滚必须配套

`maintenance.md §4` 追加 1 行版本号撤回登记（归口"回滚后合规登记"子节，**不**归口回滚步骤）

### 差异 3：草稿 / 审核 / 终稿命名

草稿：`.scheme/temp/<skill-name>迭代优化方案-<i>-draft-<yymmdd>.md`
审核：`.scheme/temp/<skill-name>迭代优化方案-<i>-review-<yymmdd>.md`
终稿：`.scheme/<skill-name>迭代优化方案-<i>-<yymmdd>.md`

（其中 `<i>` 为迭代轮次，skill-iter 多轮迭代时使用）

---

## 场景职责矩阵

### Skill 优化（Skill Optimization）

对**其他 skill**（含自身 solution-expert）出可评审、可落地的优化方案。

**区别于"业务系统优化"的关键点**：

- **目标对象是 skill 而非业务系统**——决策维度是 frontmatter / 路径 / 硬约定 / 章节编号 / references/ 演进条款等
- **必须先分类**（自身/同源/第三方/未在册），不同分类触发不同的"修改本 skill 的硬性约定"
- **知识库按需加载**：主 SKILL.md 启动时**不**加载 `references/skill-optimization-rules.md`；仅命中本场景时由主 Agent 显式 `Read`

**R-N 维度新增**：
- R-约束-N（命中知识库第 N 条硬约束）
- R-触点-N（落到 SKILL.md / references/ 的具体触点）
- R-类型-N（自身/同源/第三方/未在册分类）

**§4 风险必须覆盖**：
- 分类误判导致走错"修改本 skill 的硬性约定"
- references/ 文件被强行覆盖（违反 §1 编辑源原则）
- 知识库膨胀变成"小 SKILL.md"

**§5 回滚必须配套**：
- `maintenance.md §4` 追加 1 行版本号撤回登记（归口"回滚后合规登记"子节，**不**归口回滚步骤）

---

## Skill 优化骨架示例

```markdown
# skill-iter 新增「并发轮次」方案
- 背景：当前 10 轮顺序执行，~30min；目标：支持 N 路并行，总耗时 ≤ 10min
- 目标类型：同源 skill（skill-iter）→ 必读 skill-iter SKILL.md §修改本 skill 的硬性约定
- 命中约束：C-003（每轮独立审计日志）、C-005（轮次 trace 落 `.skill-history/`）
- 改 skill-iter/SKILL.md Step 5 + 新增 references/concurrent-rules.md
- 回滚：flag `iter.concurrent.enabled` 默认 false；任一轮失败即整轮回退
```

---

## 评审模式 vs Skill 优化

| 维度 | 评审模式 | Skill 优化 |
|------|---------|-----------|
| 产出 | 评估清单 + 风险评级 + 改进建议 | **新方案** |
| 走三阶段 | ❌ 跳过 | ✅ 必走 |
| 触发 | 用户问"评估这个方案" | 用户要求"对 X skill 出优化方案" |
