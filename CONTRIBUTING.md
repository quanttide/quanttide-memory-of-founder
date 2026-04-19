# 维护指南

## Skill 概览

本目录将资产分类相关的工作流封装为 Skill，位于 `.agents/skills/` 目录：

| Skill | 用途 | 路径 |
|-------|------|------|
| asset-classify | 资产分类 | [.agents/skills/asset-classify/SKILL.md](.agents/skills/asset-classify/SKILL.md) |

每个 Skill 的 `SKILL.md` 包含：name、description、规则、工作流步骤。

## 使用 Skill

Agent 会根据任务类型自动匹配对应的 Skill 并执行。

## 维护 Skill

### 新建 Skill

```bash
mkdir -p .agents/skills/<name>
# 创建 .agents/skills/<name>/SKILL.md
```

SKILL.md 模板：

```markdown
---
name: <name>
description: 功能描述。
---

# <name>

## 规则

- 必须遵守的约束

## 工作流

### 步骤名称

操作步骤
```

### 修改 Skill

直接编辑 `.agents/skills/<name>/SKILL.md`。

### 删除 Skill

```bash
rm -rf .agents/skills/<name>
```

## 提交规范

提交信息遵循 Conventional Commits 格式：

| 类型 | 说明 |
|------|------|
| `docs` | 文档更新 |
| `refactor` | 重构 |
| `chore` | 构建/工具 |