# 维护说明

## §1 技能定位

方案实施助手（implementation-assistant）负责将 solution-expert 产出的方案精准落地。

- **功能一**：根据方案优化 Skill
- **功能二**：根据方案修改项目代码（必须遵守 karpathy-guidelines）

## §2 修改本技能的工作流

当需要修改 `implementation-assistant/SKILL.md` 时：

1. **确认版本**：先 Read 本文件 §4 修订记录，确认当前版本号
2. **实施修改**：按照方案修改 SKILL.md
3. **版本 bump**：版本号末位 +1（如 v0.1.0 → v0.1.1）
4. **更新三处版本号**：
   - `SKILL.md` frontmatter `metadata.version`
   - `SKILL.md` frontmatter `metadata.last_updated`（当天日期）
   - `SKILL.md` 正文启动确认行
5. **追加修订记录**：在下方 §4 追加一行
6. **双处落盘**：同步到 `.claude/skills/implementation-assistant/`

## §3 karpathy-guidelines 获取流程

**默认行为**：启动时使用本地缓存的 `references/karpathy-guidelines.md`，不主动拉取最新。

**手动更新**（用户触发）：当用户要求更新 karpathy-guidelines 时，执行 Step 3 流程：

1. 从 GitHub 获取：`curl -sL "https://raw.githubusercontent.com/multica-ai/andrej-karpathy-skills/main/skills/karpathy-guidelines/SKILL.md"`
2. 覆盖保存到 `references/karpathy-guidelines.md`
3. 同步更新 `references/karpathy-guidelines-zh.md`（重新生成中文翻译）
4. 追加修订记录到 §4

## §4 修订记录

| 版本 | 日期 | 变更说明 |
|------|------|----------|
| v0.1.0 | 2026-07-27 | 初始版本，包含 Skill 优化和项目代码修改两大功能 |
| v0.1.1 | 2026-07-27 | 新增功能：用户可手动更新 karpathy-guidelines（Step 3），默认使用本地缓存 |
| v0.1.2 | 2026-07-27 | Step 3 优化：无变更时只提醒不写 maintenance.md |
