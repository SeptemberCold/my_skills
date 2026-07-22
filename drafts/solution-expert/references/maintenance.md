# Skill 维护手册

> 本文件由维护本 skill 时按需读取，**不在运行时加载**。
>
> 加载时机：
> - 修改本 skill 之前（看维护说明与最近变更）

---

## 1. 维护说明

- **维护者**：方案专家 skill 作者
- **向后兼容**：`description` 与 frontmatter 不轻易变动；章节编号稳定

## 2. 修改本 skill 的工作流

1. 读本文件，看「修订记录」确认当前版本
2. 读主 `SKILL.md`，定位要改的章节
3. 必要时读 `references/review-template.md` / `references/optimize-rules.md`（涉及 Step 4.2 / 4.3 时）
4. 改完后根据需要单处落盘、双处落盘，默认单处落盘到工作副本：
   - 工作副本：`drafts/solution-expert/`
   - 部署副本：`.claude/skills/drafts/solution-expert/`
5. 在下方「修订记录」追加一行（**末位 +1**）
6. 同步更新所有版本号出现位置：
   - `frontmatter.metadata.version`
   - `frontmatter.metadata.last_updated`
   - 正文启动确认行（`🎯 [solution-expert vX.Y.Z] ...`）

## 3. 何时拆 references/

判断信号（出现任一即可拆）：

- 主 SKILL.md 加载时上下文中相关章节占比 > 20%
- 某段规则只在 5 大场景中的 1-2 个触发
- 内容是"模板 / 契约 / 元信息"而非"决策与流程"

现有 references/：

| 文件 | 触发场景 |
|------|---------|
| `review-template.md` | 仅 Step 4.2（审核 subagent）触发 |
| `optimize-rules.md` | 仅 Step 4.3（优化 subagent）触发 |
| `maintenance.md`（本文件） | 仅修改本 skill 时读取，运行时永不加载 |

## 4. 修订记录

> **末位 +1** 是默认递增策略。跨过大版本（如 `0.3.99 → 0.4.0`）需先讨论。

| 版本 | 累计变更 |
|------|---------|
| 0.3.0 | 单场景 skill |
| 0.3.1 | 结构重组 + 21 项变更（角色、NFR、可观测性、DoD 等） |
| 0.3.2 | description 精简 + ADR 模板 + 5 场景骨架 + 评审触发条件 |
| 0.3.3 | 移除冗余 + 合并边界 + 评审模式 |
| 0.3.4 | 多轮对话 + 跨 skill 协作 + ack + DoD 收紧 |
| 0.3.5 | §3 分组 + 安全设计 + 质量准则整合 |
| 0.3.6 | 打磨：层级统一 + 互引 + 表格精简 + frontmatter |
| 0.3.7 | 边界与稳定：场景合并 + 沉默场景 + 文档归档 |
| 0.3.8 | 示例丰富：NFR / 接口 / 时序图 / 反例 / 估算方法 |
| 0.3.9 | 反面示例 +1 + Code Review 总览 + §6 基线 + §7 成本 + ADR 表格化 |
| 0.3.10 | 修订记录 + 维护说明 + checklist 重命名 + ADR 触发条件 + 变更摘要模板 |
| 0.3.11 | 三阶段流水线（草稿→审核→优化）+ 审核助手 + 优化助手 + `.scheme/temp/` 目录 |
| 0.3.12 | 拆出 references/（review-template.md + optimize-rules.md），SKILL.md 主流程瘦身，按需 Read |
| 0.3.13 | 把维护说明 + 修订记录抽到 references/maintenance.md，运行时不加载，按需 Read |