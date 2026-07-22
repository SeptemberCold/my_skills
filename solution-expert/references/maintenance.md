# Skill 维护手册

> 本文件由维护本 skill 时按需读取，**不在运行时加载**。
>
> 加载时机：
>
> - 修改本 skill 之前（看维护说明与最近变更）

---

## 1. 维护说明

- **维护者**：方案专家 skill 作者
- **向后兼容**：`description` 与 frontmatter 不轻易变动；章节编号稳定
- **references/ 演进条款**：`references/` 下的 `review-template.md` / `optimize-rules.md` / `maintenance.md` 与 SKILL.md **共用同一版本号**（v0.3.X 递增）。修改上述任一文件**必走** §🔴 硬性约定 4 件事（bump 版本号 + 追加 §4 修订记录 + 同步三处版本号 + 双处落盘）。**触发边界**：任何**非纯空白/换行**修改必触发；纯格式调整（末尾换行 / 标点修正）可不 bump 但要在 §4 修订记录 标注一行说明

## 2. 修改本 skill 的工作流（硬性 4 件事，与 SKILL.md §🔴 一字不差）

> 🔴 **本节是硬约束**，凌驾于 SKILL.md 其他规则之上。任何修改少一件事都视为修改未完成，skill-iter 会拦截。

**4 件事**：

1. **先 `Read` 本文件 + `SKILL.md`** —— 看 §4 修订记录确认当前版本号，定位要改的章节
2. **改完后追加一行**到下方「修订记录」（版本号末位 +1 + 一句话累计变更）—— **漏掉此步 = 修改未完成**
3. **同步更新所有版本号出现位置**（3 处必须保持一致）：
   - `frontmatter.metadata.version`
   - `frontmatter.metadata.last_updated`（当天日期）
   - 正文启动确认行（`🎯 [solution-expert vX.Y.Z] ...`）
4. **双处落盘 + diff 校验一致**：
   - 工作副本：`drafts/solution-expert/SKILL.md`
   - 部署副本：`.claude/skills/drafts/solution-expert/SKILL.md`
   - 校验：`diff drafts/solution-expert/SKILL.md .claude/skills/drafts/solution-expert/SKILL.md && echo "OK: 两处一致"`

涉及 Step 4.2 / 4.3 时，按需读 `references/review-template.md` / `references/optimize-rules.md`。

> 📎 详见 SKILL.md 顶部"🔴 修改本 skill 的硬性约定"节，那里是给修改者的醒目提示，本节是其完整说明。

## 3. 何时拆 references/

判断信号（出现任一即可拆）：

- 主 SKILL.md 加载时上下文中相关章节占比 > 20%
- 某段规则只在 5 大场景中的 1-2 个触发
- 内容是"模板 / 契约 / 元信息"而非"决策与流程"

现有 references/：

| 文件 | 触发场景 |
| ---- | -------- |
| `review-template.md` | 仅 Step 4.2（审核 subagent）触发 |
| `optimize-rules.md` | 仅 Step 4.3（优化 subagent）触发 |
| `maintenance.md`（本文件） | 仅修改本 skill 时读取，运行时永不加载 |
| **演进条款** | **上述 3 个文件被修改时，均触发 §🔴 硬性约定 4 件事（详见 §1 维护说明 + SKILL.md §🔴 第 1 条）** |

## 4. 修订记录

> **末位 +1** 是默认递增策略。漏记 = 修改未完成，skill-iter 会拦截。跨过大版本（如 `0.3.99 → 0.4.0`）需先讨论。

| 版本 | 累计变更 |
| ---- | -------- |
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
| 0.3.14 | 硬性约定升级：修改本 skill 必须同步追加 references/maintenance.md §4 修订记录 + 同步三处版本号 + 双处落盘（漏一条视为修改未完成） |
| 0.3.15 | 文本矛盾消除 + 跨文档引用对齐：deploy_path 统一为 `.claude/skills/drafts/solution-expert/SKILL.md`；maintenance.md §2 "6 步" → "4 件事"与 SKILL.md §🔴 对齐；§文件结构 加 .scheme/ 说明；yymmdd 示例 10722→0722；§边界条件/§不同场景/§Code Review Checklist 三处加交叉引用；§完成检查清单 重命名为"作者自检视图"显式指向 §Code Review Checklist |
| 0.3.16 | references/ 演进条款 + Step 4 流水线补全 + 文档输出规则细化 + 跨 skill 协作补 skill-iter + 边界条件评审模式显式：SKILL.md §🔴 第 1 条扩展 references/*.md 必走 4 件事；§Step 4 加草稿 300 行提示 / subagent 超时阈值 / 草稿大改 30% 回退 / temp 30 天清理（不引入 cron）；§文档输出规则 增并发 `-N` 中段命名 / archive 不带 `-N` / 复盘文档位置示例；§跨 skill 协作 首行加 skill-iter；§边界条件 评审模式显式（避免被误归入"不适用场景"）；maintenance.md §1 + §3 同步 references/ 演进条款 |
