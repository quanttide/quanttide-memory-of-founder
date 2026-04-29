# Changelog

## [0.3.0] - 2026-04-29

### Added

- context/: 从 context 子仓库接收大量文档，集中存放语境层内容
  - context/acadmics/, context/agent/, context/brand/, context/code/, context/course/
  - context/fiction/, context/health/, context/hr/, context/infra/, context/knowl/
  - context/media/, context/mkt/, context/product/, context/qtclass/, context/think/, context/write/
- bylaw/org.md: 组织章程
- context/think/: 新增 mental-models.md, situation-awareness.md, situation-level.md, refactor.md, role.md
- context/write/: 新增 brochure-style.md, story-style.md（替换 style.md）
- context/health/shame.md: 羞耻感分析
- context/media/: 新增 content.md, growth.md, role.md, strategy.md, workflow.md
- gallery/sdr-write-style.md: SDR 写作风格
- profile/think_pattern.md: 思考模式文档

### Removed

- context/asset/category.md, context/asset/index.md
- context/qtcloud-think/ixd.md
- context/write/style.md（拆分为 brochure-style.md 和 story-style.md）
- example/qtcloud-think/cogitive_prism.html

### Changed

- AGENTS.md: 更新工作原则
- roadmap/qtcloud/qtcloud-think.md: 更新愿景与路线图

### Daily

- journal/: 2026-04-26 ~ 2026-04-29 日报归档

## [0.2.2] - 2026-04-26

### Changed

- qtcloud-think.md: 重构愿景和蓝图分离
  - vision/qtcloud/: 重构愿景层（default, qtadmin, qtcloud-* 系列）
  - roadmap/qtcloud/qtcloud-think.md: 核心功能、方法论、冷启动路径
  - tutorial/: 新增认知工程和驾驭工程教程
  - cognitive_engineering.md: 认知工程方法论
  - harness_engineering.md: 驾驭工程教程

### Structure

- Roadmap 结构调整: roadmap/ 目录结构优化
- Vision 结构调整: 愿景层与蓝图层分离

## [0.2.1] - 2026-04-25

### Changed

- qtcloud-think.md: 重构愿景和蓝图分离
  - vision/qtcloud/qtcloud-think.md: 定位、原则、哲学、交互设计、运行循环、独特价值、愿景
  - roadmap/qtcloud/qtcloud-think.md: 工作流、核心功能、方法论、冷启动路径

## [0.2.0] - 2026-04-25

### Added
- qtcloud-think.md: 整合思考云产品愿景（关联联想、高频问题、思考模型）
- visualization.md: 数据可视化方案
- sync.md: 协作同步机制
- internal-training.md: 内部培训蓝图
- contract.yaml: 契约配置文件

### Changed
- 重构分类理论文档：why_category_theory_work.md, category_theory_on_story.md
- 优化资产规范：asset.md → asset_spec.md
- 扩展写作风格指南：fiction_style.md, style.md
- 更新日报归档（2026-04-19 ~ 2026-04-25）

### Structure
- 新增蓝图层：roadmap/ 目录结构
- 重构语境层：context/data/, context/think/
- 优化愿景层：vision/qtcloud/

### Process
- 新增技能：逆向拆解代码生成文档 SKILL
- 完善工作流：workflow.md, delib.md
- 归档废弃文件：gallery.md, qtcloud-asset.md

## [0.1.0] - 2026-04-19

### Added
- asset-classify skill: 资产分类技能，包含 Type/Category 双轴治理模型
- CONTRIBUTING.md: 维护指南
- AGENTS.md: Agent 配置索引

### Changed
- 重构分类结构，日志/蓝图/语境/愿景/归档六类

### Structure
- .agents/skills/asset-classify/SKILL.md
- CONTRIBUTING.md
- AGENTS.md