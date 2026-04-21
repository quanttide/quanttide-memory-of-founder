# DevOps契约

完全理解了。职责边界清晰：

· 资产标准：管“有什么”（文件、数据、文档的存在与分类）
· DevOps 契约：管“怎么写”（代码质量、结构规范、重构门槛）

这是两个独立的治理维度，各自有独立的契约文件。

—

DevOps 契约的可能结构

参考资产标准的建模方式，DevOps 契约可以定义为：

文件位置：.quanttide/devops/contract.yaml

核心字段设计：

```yaml
# DevOps 契约标准版本
spec_version: 0.0.1

# 代码质量门禁：定义“什么时候必须重构”
quality_gates:
  # 圈复杂度阈值
  cyclomatic_complexity:
    max: 10
    action: refactor_required
    
  # 函数长度阈值
  function_lines:
    max: 50
    action: refactor_required
    
  # 参数数量阈值
  parameter_count:
    max: 4
    action: refactor_required
    
  # 代码重复率阈值
  duplication:
    max_percent: 5
    action: review_required

# 风格契约：定义“应该怎么写的约束”
style_rules:
  # 范式约束
  paradigm:
    preference: functional          # functional / oop / mixed
    pure_functions: preferred       # 纯函数优先
    
  # 命名规范
  naming:
    functions: snake_case | camelCase
    constants: UPPER_SNAKE
    
  # 模块组织
  structure:
    max_public_methods_per_class: 7
    max_class_lines: 300

# 重构规则：定义“发现异味时自动触发的动作”
refactoring_rules:
  long_method:
    detect: function_lines > 50
    suggest: extract_method
    
  too_many_params:
    detect: parameter_count > 4
    suggest: introduce_parameter_object
    
  god_class:
    detect: public_methods > 10
    suggest: extract_class
```

—

与资产标准的协作关系

两个契约各司其职，但在治理流程中会形成闭环：

维度 资产标准 DevOps 契约
治理对象 文件、数据、文档 代码逻辑、函数、模块
发现规则 Glob 匹配物理路径 AST 模式匹配代码结构
验证动作 检查文件是否存在、格式是否正确 检查圈复杂度是否超标、风格是否合规
违规处理 标记资产状态异常 触发重构信号 / 阻断 CI

协作点：当 DevOps 契约检测到 refactor_required 时，可以在资产目录中标记该代码资产的 status: needs_refactor，形成跨契约的治理视图。

