在计算机语境中，这三个词的区别很清晰：

· State（状态）：指系统或组件在某个特定时刻的完整快照。它包含所有内部属性的值，决定了系统下一步的行为。比如“TCP连接”的 State 可以是 ESTABLISHED、CLOSED；一个UI按钮的 State 可以是“启用”或“禁用”。
· Status（状态码/状况）：通常是对 State 的简要总结或报告，侧重于当前的操作结果或所处阶段，多为固定枚举值。比如HTTP的 404 Not Found、任务的 SUCCESS / FAILED。相对于 State，Status 更关注“处于什么阶段/结果”，而不是所有内部细节。
· Stage（阶段）：强调在流程或生命周期中的步骤，有明确的前后顺序。例如：编译流程的 parse → analyze → codegen；DevOps 流水线的 Dev → Test → Staging → Prod。Stage 不关心内部属性快照，只关心当前位于哪一步。

简单区分：State 回答“现在内部是什么样的？”；Status 回答“现在办事办到哪一步了/结果是成功还是失败？”；Stage 回答“现在处于整个流程中的第几步？”

需要我举例说明同一个系统中这三个概念是如何共存的吗？