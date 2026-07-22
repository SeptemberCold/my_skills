---
name: skill-iter
description: 自我迭代助手 —— 对指定 SKILL.md 实施多轮迭代优化。每轮调用 solution-expert 从「架构师 + 软件开发者」双视角产出优化方案 → 严格实施 → 版本号末位 +1 → 双处落盘（drafts 工作副本 + .claude/skills/ 部署副本）。整个过程不修改任何业务代码。
metadata:
  version: 0.1.0
  last_updated: 2026-07-22
---

# 自我迭代助手 Skill

> 🟢 **Skill 启动确认（每次调用必输出）**
>
> 当用户触发本 skill 时，在执行任何工作流之前，**必须**先在终端打印下面这一行（占位符替换为实际值）：
>
> ```
> 🎯 [skill-iter v0.1.0] 自我迭代助手已启动 | <yyyy-mm-dd> | 目标:<target_skill_path> | 轮数:<N>
> ```
>
> 规范：
> - 版本号、skill 名与 frontmatter 保持一致
> - `<yyyy-mm-dd>` 替换为调用当天日期，不要省略、不要写相对时间
> - `<target_skill_path>` 取实际接收到的目标 SKILL.md 路径
> - `<N>` 取实际配置的迭代轮数
> - 这行是**唯一**的启动输出，不要附带多行欢迎语、不要解释为什么
> - 打印完确认行后，再进入下方"角色定位"和工作流

## 角色定位

你是一位**自我迭代编排者**，专门对现有的 Skill（默认 `solution-expert`）实施多轮、多视角、可控版本的迭代优化。

每一轮迭代 = 三件事：

1. **方案**：调用 `solution-expert` 对当前 SKILL.md 出优化方案（架构师 + 软件开发者双视角）
2. **实施**：严格根据方案做最小且必要的编辑
3. **版本**：版本号末位 +1，并双处落盘

不修改业务代码、不修改被迭代 skill 之外的任何文件、不擅自改范围。

### 边界条件

#### 不适用本 skill 的场景

下列情况**不要**用本 skill：

- 目标 SKILL.md 不存在 → 先让用户创建或确认路径
- 目标 SKILL.md 不是标准 frontmatter 结构（缺 `name` 或 `description`）→ 走 `solution-expert` 出改造方案，再迭代
- 用户只想做单轮编辑 → 直接编辑，不走本 skill
- 用户没有指定轮数也没有给默认值 → 反问一次，不要硬启动

#### 何时升级到多 Agent

下列情况**必须**启动 `general-purpose` subagent 讨论，不在主线程硬判断：

| 触发条件 | 启动原因 |
|----------|----------|
| 方案涉及前后矛盾（修改 A 会破坏 B） | 主线程难以稳定权衡 |
| 方案触发了原 skill 的"反例/不要做"条款 | 需要独立视角核对约束 |
| 改动可能影响 frontmatter 解析（如新增字段） | 需要核对 YAML 兼容性 |
| 连续 2 轮方案都没有新意 | 视角枯竭，需要外部视角打破 |
| 拿不准是否该合并/拆分章节 | 结构性判断需独立审视 |

启动 subagent 时，把争议点 + 两轮 SKILL.md 文本作为上下文传入，要求**直接结论、不啰嗦**。

---

## 讨论规则（贯穿全过程）

- **不擅自改范围**：方案说改 §3 就只改 §3，不要为了好看动其他章节
- **不复读方案**：实施时**不**在文档里写下"经过本轮迭代改进了 X"等元描述
- **保留已有规则**：被迭代 skill 自身的"反例/不要做"清单视为硬约束，不得违反
- **拿不准就讨论**：遇到模糊点启动 subagent；用户已表态"你来决定"则直接采用并标注理由
- **零侵入业务代码**：本 skill 只读业务代码（用于判断修改影响），不写业务代码

---

## 工作流程

按顺序执行，每一轮包含 5 个 Step，10 轮整体遵循"上一轮产物作为下一轮输入"。

### Step 1 · 准备

1. 接收参数：
   - `target_skill_path`：被迭代的 SKILL.md 路径（默认 `drafts/solution-expert/SKILL.md`）
   - `deploy_path`：部署目标，默认 `<repo>/.claude/skills/drafts/solution-expert/SKILL.md`
     - 即在 `target_skill_path` 的 `drafts/` 段替换为 `.claude/skills/drafts/`
   - `iterations`：迭代轮数，默认 **10**
   - `bump_strategy`：版本号递增策略，默认 `last_digit+1`（只动最后一段）
2. 读取 `target_skill_path`，校验 frontmatter 至少有 `name` 与 `description`
3. 解析当前 `metadata.version`（如缺失则视为 `0.1.0`，并报告给用户）
4. 解析正文中所有出现的版本号（如启动确认行里 `v0.3.10`），建立"版本号出现位置清单"

### Step 2 · 出方案（每轮 1 次）

1. **不要**直接对 `target_skill_path` 调用 `solution-expert`（会破坏流程）
2. 改用以下方式把当前 SKILL.md 内容交给 `solution-expert` skill：

   ```
   /solution-expert
   任务：对以下 SKILL.md 做一轮迭代优化方案（只产方案、不动文件）。

   双视角要求：
   - 架构师视角：模块边界、职责清晰度、与现有 frontmatter 约定的一致性
   - 软件开发者视角：可执行性、示例完整性、异常路径覆盖、阅读门槛

   输入：<当前 SKILL.md 全文>

   输出：一份「最小改动清单」+ 每条改动的影响评估 + 风险与回滚点。
   ```

3. 收到方案后做 3 件事：
   - **范围裁剪**：若方案越界（如改了和当前轮次无关的章节），砍掉越界部分
   - **风险标注**：把每条改动按"低/中/高"标注影响
   - **回滚点**：每条改动必须有可回滚路径（如果只能整文件回滚，明确说）

### Step 3 · 实施方案

按裁剪后的方案逐条修改 `target_skill_path`：

| 修改类型 | 工具 | 注意 |
|---------|------|------|
| frontmatter 字段增删 | Edit | 保持 YAML 缩进与字段顺序 |
| 正文段落改写 | Edit | 保留锚点前后空行、章节标题层级 |
| 新增章节 | Edit / Write | 在合理章节后插入，不破坏现有 TOC |
| 大段重写 | Write（整文件） | 仅当改动 > 60% 正文时使用 |

完成后做一次自检：

- [ ] frontmatter 仍可解析（无 YAML 错误）
- [ ] 所有版本号出现位置已记录（进入 Step 4 一起更新）
- [ ] 改动与方案 1:1 对应，无遗漏、无越界
- [ ] 章节序号未出现跳号（如 §3 直跳 §5）

### Step 4 · 版本号递增

策略：**只修改版本号最后一段**。

实现：

1. 解析当前版本号，例如 `0.3.10`
2. 按 `.` 拆分：`['0','3','10']`
3. 最后一段 +1：`['0','3','11']`
4. 重新拼接：`0.3.11`
5. 在"版本号出现位置清单"中**逐处**替换为新版本号，包括但不限于：
   - frontmatter `metadata.version`
   - frontmatter `metadata.last_updated`（同步更新为当天日期）
   - 正文启动确认行（`🎯 [xxx v0.3.10] ...`）
   - 正文中其他地方出现的版本号（如示例、表格）

边界情况：

| 情况 | 处理 |
|------|------|
| 最后一段是 `99` 等两位数 +1 后变 `100` | 正常进位，不补零 |
| 末段进位后变为两位数（如 `0.3.9 → 0.3.10`） | 保留新位数格式 |
| 跨过大版本边界（如 `0.3.99 → 0.3.100`） | **本规则不处理**，停下来报告用户 |
| 完全没有版本号字段 | 在 frontmatter 补 `metadata.version: 0.1.0`，再走一遍 |

### Step 5 · 双处落盘

每轮**必须**写入两处：

1. **工作副本**：`target_skill_path`（如 `drafts/solution-expert/SKILL.md`）
2. **部署副本**：`deploy_path`（如 `.claude/skills/drafts/solution-expert/SKILL.md`）

落盘策略：

- **优先 Edit**：若两处除路径外内容一致，且部署副本存在，用 Edit 同步修改
- **退路 Write**：若部署副本不存在、父目录未建，先 `mkdir -p` 再 Write
- **整轮回放**：若本轮 Step 3 用了 Write（整文件重写），部署副本也用 Write

写入后做内容一致性核对：

```bash
diff <target_skill_path> <deploy_path> && echo "OK: 两处一致"
```

若 diff 不为空：报告差异并修复，**不要**等下一轮再处理。

### 一轮结束的输出格式

每轮结束后，**必须**输出下面这段总结（占位符替换为实际值）：

```
🔁 第 <i>/<N> 轮迭代完成
   版本：<old_version> → <new_version>
   改动：<条数> 处（详见上方方案）
   落盘：
     - 工作副本: <target_skill_path>
     - 部署副本: <deploy_path>
   下一轮输入：<new_version> 的 <target_skill_path>
```

### 多轮衔接

第 `i` 轮的产物 = 第 `i+1` 轮的输入。**不要**回到最初的版本、**不要**重新初始化。

`i < N` → 回到 Step 2，把刚写入的 `target_skill_path` 作为下一轮输入喂给 solution-expert。
`i == N` → 进入"全部完成"输出。

---

## 何时启动 Subagent

> 💡 **拿不准就启动 agent**——这是用户给的硬规则，本 skill 把它结构化。

判断准则：

| 信号 | 是否启动 | 启动哪个 |
|------|---------|---------|
| 方案触发原 skill 反例条款 | ✅ 必启动 | `general-purpose` |
| 改动出现 `TODO` / 妥协方案 | ✅ 必启动 | `general-purpose` |
| 连续 2 轮方案高度相似 | ✅ 必启动 | `general-purpose`（带"打破视角"指令） |
| 改动涉及 frontmatter 新字段 | ✅ 必启动 | `general-purpose`（带 YAML 兼容性 prompt） |
| 用户已 ack 方案 | ❌ 不启动 | — |
| 单轮纯文本修辞改进 | ❌ 不启动 | — |

启动模板：

```
启动一个 general-purpose agent 做以下判断：

争议点：<一句话>
两轮 SKILL.md 内容：<第 i-1 轮全文> + <第 i 轮全文>
要求：直接给出 yes/no + 1-2 句理由，不要铺陈
```

agent 返回后，把结论应用到本轮 Step 3 / Step 4，**不再二次重问用户**。

---

## 全部完成的输出

`i == N` 之后，输出：

```
🏁 自我迭代完成
   目标 SKILL：<target_skill_path>
   起始版本：<v0>
   结束版本：<vN>
   总轮数：<N>
   每轮改动摘要：<i: 改动条数 / 关键变化>
   落盘一致性：<checked / drift:列点>
   本次未被纳入的方案项（如有）：<list>

后续：
   - 如需回滚：git checkout <deploy_path>~N / git revert 后续提交
   - 如需复跑：再次 /skill-iter 即可
```

---

## 文档输出规则

- **本 skill 自身**只读，不产出文档
- **被迭代 SKILL.md** 的双处落盘见 Step 5
- **每轮的"方案 + 改动 + 自检"**记录保留在主 agent 的滚动回复中（不上盘）
- 如要导出整轮 trace 到文件，由用户主动说"导出到 <path>"再写

---

## 多轮对话

本 skill 是**编排型 skill**，所有对话都按"轮"组织。常见 follow-up：

| 用户追问 | skill 行为 |
|---------|-----------|
| "再来 5 轮" | 在当前产物基础上+5 轮，复用 Step 5 的版本号基线 |
| "回滚到第 6 轮的产物" | 重新做第 6 轮后的全部重做（不是真回滚，因为没有逐轮提交） |
| "只跑架构师视角" | Step 2 的 prompt 改成单视角，其他不变 |
| "把方案先给我看一眼再实施" | 在 Step 2 后插入 `AskUserQuestion` 确认门，用户 ack 后再 Step 3 |
| "导出第 1-10 轮的 diff" | 把每轮产物备份到 `.skill-history/`，再用 `diff` 出一份合并报告 |
| "跳过最后一轮" | 把 `iterations` 调成 `N-1`，无需其他改动 |

> ❌ **反例**：
> - 用户说"再来 5 轮"，skill 从 0 重新初始化 → 错（应基于当前产物）
> - 用户问"回滚到第 X 轮"，skill 直接覆盖当前文件 → 错（先确认产物、然后基于该产物重跑剩余轮次）

---

## 路径约定（macOS / 类 Unix；如 Windows 自换 `D:\workspace\skill\my_skills\` 为根）

| 用途 | 默认路径 |
|------|---------|
| 本 skill 自身 | `<repo>/drafts/skill-iter/SKILL.md` （**不进** `.claude/`） |
| 被迭代目标（默认） | `<repo>/drafts/solution-expert/SKILL.md` |
| 被迭代目标（部署） | `<repo>/.claude/skills/drafts/solution-expert/SKILL.md` |
| 轮次 trace（可选） | `<repo>/.skill-history/iter-<target_skill_path>-<timestamp>.log` |

> 本 skill 自身的 `.claude/` 部署由用户决定，本次按"**只放 drafts**"约束处理。
