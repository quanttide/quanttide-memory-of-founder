# 智能体

一种新的架构思路：默认偏移模型做，规则引擎判断，结果差就找上级模型修正。

这需要对模型能力有充分理解，而且有合适的规则可以调度。

具体可以使用不同维度的测评分数为基准，同时结合自定义的维度和测评迭代。

聪明模型要限制，而且要对比聪明模型和笨蛋模型积累经验进化。

高风险任务要人类二次核验。

伪代码示例：

out = l0.run(task)
signals = rules.score(task, out)

if signals.risk == ”high“ or not signals.schema_valid or signals.confidence < τ_low:
    out = l1.correct(task, out, signals)
    log.write(task, out0=out, signals=signals, corrected=out)
    budget.consume()
elif signals.confidence < τ_mid and budget.left() > 0:
    out = l1.review(task, out, signals)
    log.write(...)

自动生成规则 RuleGen 是必要的。

GLM 适合发散，DeepSeek 适合对齐，Kimi 适合收敛。

## 对齐

对齐这个行为大体可以分为 3 个阶段：澄清、发散和收敛。DeepSeek、GLM 和 Kimi 分别是这 3 个阶段最合适的模型。（PS：DeepSeek 的澄清能力在下降，发散能力在上升。）

其中，澄清阶段得到的通常是意图，发散阶段得到的是洞察，而收敛阶段得到的是路线图。

