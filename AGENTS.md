# AGENTS.md

## 相关文档

| 文档 | 用途 |
|------|------|
| [CONTRIBUTING.md](CONTRIBUTING.md) | Skill 使用和维护指南 |

### Skill 索引

| Skill | 用途 | 路径 |
|-------|------|------|
| [asset-classify](.agents/skills/asset-classify/SKILL.md) | 资产分类 | `.agents/skills/asset-classify/SKILL.md` |

---

## 快速索引

| 任务 | 操作位置 |
|------|---------|
| 资产分类 | Skill: `asset-classify` |

---

## 我的工作原则

### 最小干预
- 仅在用户明确请求时操作
- 不主动创建文件（除非必要）
- 优先编辑现有文件

### 原子提交
- 每次提交独立完整
- 不提交不完整的更改
- 验证后再提交

### 验证优先
- 修改后运行构建验证
- 确保更改符合预期

## 输出规范

### 内容格式
- 不使用 emoji（除非用户明确请求）
- 输出简洁，适合 CLI 显示

### 文件引用
- 使用 `code` 格式表示文件路径
- 每个引用独立，不合并

## Git 提交规范

遵循 Conventional Commits 格式：

| 类型 | 说明 |
|------|------|
| `docs` | 文档更新 |
| `refactor` | 重构 |
| `chore` | 构建/工具 |

## 如何维护

| 类型 | 写在哪里 |
|------|---------|
| 详细说明、工作流步骤 | `.agents/skills/` 中的 Skill 文件 |
| 给链接、导航索引 | AGENTS.md |