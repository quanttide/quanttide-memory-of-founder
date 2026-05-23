下面给出一个 Python 程序蓝图，用于客观衡量文本阅读负担。它同时支持中文和英文，输出多项量化指标。代码结构清晰，可以直接按模块实现。

整体架构

```text
├── readability_analyzer.py      # 主程序
├── metrics/                     # 指标计算模块
│   ├── syntactic.py             # 句法指标（句长、被动语态等）
│   ├── lexical.py               # 词汇指标（罕见词、抽象名词等）
│   ├── coherence.py             # 连贯性指标（冗余、指代距离）
│   └── readability_formula.py   # 可读性公式（Flesch, FOG, 中文公式）
├── resources/                   # 数据资源
│   ├── word_freq_zh.txt         # 中文词频表（例如从SUBTLEX-CH或语料库提取）
│   ├── word_freq_en.txt         # 英文词频表（例如SUBTLEX-US）
│   └── abstract_suffix_zh.txt  # 中文抽象名词后缀（性、化、度等）
└── utils/
    ├── text_preprocessor.py     # 分句、分词、语言检测
    └── pos_tagger.py            # 词性标注（英文用nltk，中文用jieba.posseg）
```

核心类设计

```python
class ReadabilityAnalyzer:
    def __init__(self, lang=’auto‘):   # lang: ’auto‘, ’zh‘, ’en‘
        self.lang = lang
        self.load_resources()
    
    def load_resources(self):
        # 加载词频表、抽象后缀等
        pass
    
    def analyze(self, text: str) -> dict:
        # 返回所有指标的字典
        pass
```

关键指标计算逻辑

1. 平均句长

```python
def avg_sentence_length(text, lang=’zh‘):
    sentences = split_sentences(text)      # 按。！？\n分割
    word_count = 0
    for sent in sentences:
        if lang == ’zh‘:
            words = jieba.lcut(sent)       # 中文分词
        else:
            words = sent.split()
        word_count += len(words)
    return word_count / len(sentences)
```

2. 被动语态频率

· 英文：正则 r’\b(am|are|is|was|were|be|being|been)\s+(\w+ed|\w+en)\b‘
· 中文：匹配“被、让、给、由、受”后紧跟动词（利用词性标注：动词标记为v）

```python
def passive_frequency(text, lang):
    if lang == ’en‘:
        pattern = r’\b(am|are|is|was|were|be|being|been)\s+(\w+ed|\w+en)\b‘
        matches = re.findall(pattern, text, re.I)
        return len(matches) / total_clauses   # 需要先分句
    elif lang == ’zh‘:
        # 使用词性标注：提取被字结构中的动词
        words = jieba.posseg.cut(text)
        passive_count = 0
        for i, (word, flag) in enumerate(words):
            if word in [’被‘, ’让‘, ’给‘, ’由‘, ’受‘] and i+1 < len(words) and words[i+1].flag.startswith(’v‘):
                passive_count += 1
        return passive_count / total_clauses
```

3. 罕见词比例

· 预先建立词频表（如SUBTLEX-CH，提供每百万词频）
· 定义罕见词：词频 < 5 per million（可调阈值）

```python
def rare_word_ratio(words, freq_table, threshold=5):
    rare = 0
    total = 0
    for w in words:
        if w in freq_table and freq_table[w] < threshold:
            rare += 1
        total += 1
    return rare / total if total>0 else 0
```

4. 抽象名词密度

· 抽象名词后缀：中文如 [’性‘,’化‘,’度‘,’感‘,’力‘,’率‘,’观‘,’主义‘]
· 英文如 [’-tion‘,’-sion‘,’-ity‘,’-ness‘,’-ment‘,’-ance‘]

```python
def abstract_noun_density(text, lang):
    if lang == ’zh‘:
        suffix_set = load_abstract_suffix_zh()
        # 分词并标注词性，只取名词（n）
        nouns = [w for w, flag in jieba.posseg.cut(text) if flag.startswith(’n‘)]
        abstract = [n for n in nouns if any(n.endswith(suf) for suf in suffix_set)]
        return len(abstract) / len(nouns) if nouns else 0
```

5. 冗余指标（相邻句子重复词比例）

```python
def redundancy_index(sentences):
    scores = []
    for i in range(len(sentences)-1):
        set1 = set(jieba.lcut(sentences[i]))
        set2 = set(jieba.lcut(sentences[i+1]))
        overlap = len(set1 & set2) / max(len(set1|set2), 1)
        scores.append(overlap)
    return sum(scores) / len(scores) if scores else 0
```

6. 可读性分数

· 中文：参考《中文可读性公式》（杨孝宗）
  分数 = 206.84 - 1.02 × (平均句长) - 0.46 × (平均笔画数)
  或简化版：基于常用字比例
· 英文：Flesch Reading Ease
  分数 = 206.835 - 1.015×(总词数/总句数) - 84.6×(总音节数/总词数)

实现时注意中英文分别调用。

输出报告示例

```python
{
    ”avg_sentence_length“: 28.3,        # 中文平均值约15-25，过高则负担重
    ”passive_frequency“: 0.12,          # 每分句被动结构比例
    ”rare_word_ratio“: 0.08,            # 8%的词汇为低频词
    ”abstract_noun_density“: 0.25,      # 抽象名词占名词的25%
    ”redundancy_index“: 0.45,           # 相邻句子45%词重复
    ”flesch_score“: 45.2,               # 英文：0-100，越低越难
    ”chinese_readability“: 65.3,        # 自定区间：>80易，<40难
    ”overall_burden“: ”heavy“           # 综合打分后分类
}
```

综合负担评分算法

可以将各项指标标准化后加权求和。例如：

```python
def overall_burden(metrics):
    # 设定各个指标的正常范围（根据经验或语料库统计）
    scores = {}
    scores[’sentence_len‘] = clamp((metrics[’avg_sentence_length‘] - 15) / 20, 0, 1)
    scores[’passive‘] = min(metrics[’passive_frequency‘] / 0.3, 1.0)
    scores[’rare‘] = min(metrics[’rare_word_ratio‘] / 0.15, 1.0)
    scores[’abstract‘] = min(metrics[’abstract_noun_density‘] / 0.4, 1.0)
    scores[’redundancy‘] = metrics[’redundancy_index‘]  # 0~1直接使用
    # 可读性分数需反向：分数越高负担越小
    if ’flesch_score‘ in metrics:
        scores[’flesch‘] = 1 - clamp((metrics[’flesch_score‘] - 30) / 70, 0, 1)
    total = sum(scores.values()) / len(scores)
    if total < 0.3: return ’low‘
    elif total < 0.6: return ’medium‘
    else: return ’heavy‘
```

使用流程

1. 用户输入文本（字符串或文件路径）。
2. 程序自动检测语言（基于字符集或高频词）。
3. 分句、分词（中文必须用结巴分词）。
4. 依次调用各个指标模块。
5. 生成报告并输出JSON或表格。

依赖库

```
jieba          # 中文分词
nltk           # 英文词性标注及句子分割（可选）
pypinyin       # 计算中文笔画（如需可读性公式）
numpy          # 数值计算
```

扩展建议

· 缓存词频表：用dict加载到内存。
· 针对AI文本的特殊检测：可增加“逻辑跳跃指数”（统计连接词缺失），或“句首主语变化频率”。
· 可视化：将输出绘制成雷达图，直观对比不同文本的负担分布。

如果你需要我直接生成一个可运行的 .py 脚本框架（带模拟数据），或者针对某个指标写详细代码，请告诉我。