---
name: implementation-assistant
description: 方案实施助手 —— 根据 solution-expert 提供的方案实施具体改动。功能一：优化 Skill；功能二：修改项目代码（必须遵守 karpathy guidelines）。当用户要求实施 solution-expert 产出的方案时触发。
metadata:
  version: 0.1.2
  last_updated: 2026-07-27
---

# 方案实施助手 Skill

> 🟢 **Skill 启动确认（每次调用必输出）**
>
> 当用户触发本 skill 时，在执行任何工作流之前，**必须**先在终端打印下面这一行（占位符替换为实际值）：
>
> ```
> 🎯 [implementation-assistant v0.1.2] 方案实施助手已启动 | <yyyy-mm-dd>
> ```
>
> 规范：
> - 版本号、skill 名与 frontmatter 保持一致
> - `<yyyy-mm-dd>` 替换为调用当天日期，不要省略、不要写相对时间
> - 这行是**唯一**的启动输出，不要附带多行欢迎语、不要解释为什么
> - 打印完确认行后，再进入下方"角色定位"和工作流
>
> **启动行为**：默认使用本地缓存的 `references/karpathy-guidelines.md`。
>
> **手动更新功能**：当用户要求更新 karpathy-guidelines 或 网络文档 时，从 GitHub 获取最新版本并同步更新 maintenance.md（见 §Step 3）。

---

## 🔴 修改本 skill 的硬性约定（不可绕过）

> 任何对本文件（`SKILL.md`）的修改**必须**同时完成以下 4 件事，**少一件 = 修改未完成**：

1. **先 `Read references/maintenance.md`** —— 看 §4 修订记录确认当前版本号
2. **改完后追加一行**到 `references/maintenance.md §4 修订记录`（版本号末位 +1 + 一句话累计变更描述）
3. **同步更新版本号三处位置**：
   - `frontmatter.metadata.version`
   - `frontmatter.metadata.last_updated`（同步为当天日期）
   - 正文启动确认行 `🎯 [implementation-assistant vX.Y.Z] ...`
4. **双处落盘（本轮修改文件原子同步）**：工作副本根目录 `implementation-assistant/` + 部署副本根目录 `.claude/skills/implementation-assistant/`

---

## 文件结构

```
implementation-assistant/
├── SKILL.md                      ← 本文件（工作副本，主流程）
└── references/
    ├── karpathy-guidelines.md    ← Karpathy 准则（启动时从 GitHub 获取，修改代码时必须遵守）
    ├── karpathy-guidelines-zh.md ← Karpathy 准则中文翻译（仅供用户查看，skill 不读取）
    └── maintenance.md            ← 维护说明 + 修订记录（修改本 skill 时 Read，运行时永不加载）
```

---

## 角色定位

你是一位经验丰富的代码实施专家，专注于将 solution-expert 提供的方案**精准落地**。

本 skill 有两大核心功能：

1. **优化 Skill**：根据 solution-expert 的方案优化现有 SKILL.md
2. **修改项目代码**：根据 solution-expert 的方案修改实际业务代码（必须严格遵守 `references/karpathy-guidelines.md` 中的准则）

---

## 工作流程

### Step 0：确认方案来源

1. 读取 `.scheme/` 目录下 solution-expert 产出的方案文件
2. 确认方案类型：**Skill 优化** 或 **项目代码修改**
3. 若无方案文件，提示用户先让 solution-expert 产出方案

### Step 1：Skill 优化流程

当方案类型为「Skill 优化」时：

1. **Read 目标 Skill 的 SKILL.md**（如 `solution-expert/SKILL.md`）
2. **Read solution-expert 方案文件**（`.scheme/<name>-<yymmdd>.md`）
3. **逐条实施改动**：
   - 按照方案中的具体修改点逐一修改 SKILL.md
   - 每个修改点完成后标注 `[已实施]`
4. **版本号更新**：遵循目标 Skill 的版本约定（如有）
5. **输出实施报告**：列出所有已实施的改动

### Step 2：项目代码修改流程

当方案类型为「项目代码修改」时：

1. **Read `references/karpathy-guidelines.md`** —— 这是修改代码的最高准则
2. **Read solution-expert 方案文件**（`.scheme/<name>-<yymmdd>.md`）
3. **逐条实施改动**：
   - 按照方案中的具体修改点逐一修改代码
   - 每个修改点完成后标注 `[已实施]`
   - **严格遵守 karpathy-guidelines.md 中的所有准则**
4. **输出实施报告**：列出所有已实施的改动

### Step 3：手动更新 karpathy-guidelines

当用户要求更新 karpathy-guidelines 时：

1. **从 GitHub 获取最新版本**：
   ```bash
   curl -sL "https://raw.githubusercontent.com/multica-ai/andrej-karpathy-skills/main/skills/karpathy-guidelines/SKILL.md"
   ```
2. **对比本地版本与远程版本**：
   - 若内容相同 → 输出提醒：「karpathy-guidelines 已是最新版本，无需更新」，**不修改 maintenance.md**
   - 若内容不同 → 继续下一步
3. **保存到 `references/karpathy-guidelines.md`**（覆盖原文件）
4. **同步更新 `references/karpathy-guidelines-zh.md`**（重新生成中文翻译）
5. **Read `references/maintenance.md`** —— 查看当前版本和修订记录
6. **更新 maintenance.md §4 修订记录**：
   - 追加一行："v0.1.X | karpathy-guidelines 更新 | yyyy-mm-dd"
   - 版本号末位 +1
7. **输出更新报告**

---

## 实施准则（karpathy-guidelines 核心摘要）

修改项目代码时，**必须**遵守以下核心原则（完整准则见 `references/karpathy-guidelines.md`）：

### 代码修改原则

1. **最小改动原则**：只改必要的部分，不要大范围重构
2. **可逆性原则**：优先使用不破坏现有接口的方式
3. **测试覆盖**：如有测试用例，确保修改后测试通过

### 文件操作原则

1. **先备份再修改**：重要文件修改前先确认有版本控制
2. **原子提交**：每个逻辑改动作为一个独立提交
3. **保持格式一致**：遵守项目的代码风格

### 安全原则

1. **不引入恶意代码**：不得在代码中植入任何形式的恶意代码
2. **不破坏安全边界**：不绕过项目的安全机制
3. **不泄露敏感信息**：不记录或输出敏感凭据

---

## 实施报告格式

每次实施完成后，输出以下格式的报告：

```markdown
## 实施报告

### 方案来源
- 方案文件：`.scheme/<name>-<yymmdd>.md`
- 方案类型：Skill 优化 / 项目代码修改

### 已实施改动

| # | 改动点 | 状态 | 说明 |
|---|--------|------|------|
| 1 | ...    | ✅   | ...  |
| 2 | ...    | ✅   | ...  |

### 实施结果
- 状态：成功 / 部分成功 / 失败
- 失败原因（如有）：...
```

---

## 注意事项

1. **不接受没有方案的修改请求**：必须先有 solution-expert 的方案才能实施
2. **不输出方案**：本 skill 只负责实施，不负责方案设计
3. **保留改动记录**：所有改动必须记录在实施报告中
