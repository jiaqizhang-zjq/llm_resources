---

# Boosting LLM via Learning from Data Iteratively and Selectively

## 摘要

## 背景
训练数据的复杂度+多样性

## 动机

## 核心工作
复杂度：加指令ppl/不加指令ppl；多样性：tf-idf；数量？

## 相关工作

## 核心方法

## 基线
1. vanilla
2. longest
3. deita
4. superfiltering
5. graphfilter

## 数据集

## 效果

## 消融实验

## 结论

---

# Long Is More for Alignment: A Simple but Tough-to-Beat Baseline for Instruction Fine-Tuning 

## 摘要
公认指令微调依赖高质量数据；本文展示一个特别简单的策略：选择1k个回复最长的指令效果一贯超过那些复杂的方法；用gpt4、palm做llm as judge评估。对这些1k个加一些强化后效果会更好。
## 背景
一个公认的直觉是：ift可以从少量高质量数据中收益（lima只需要1k个样本；alpaca需要52k）；虽然质量很重要，但是高质量数据的特性仍然不清晰；本文回顾构造指令微调数据的典型工作。
## 动机
最近的工作lima选择的样本包含比alpaca更长的回复、并且rlhf和dpo类工作也会使得模型的回复更长。文章测试直接把最长回复作为一个简单的策略，来选择高质量数据集。直接抽取1k，效果比lima和alpagasus效果还要好；这种方法的效果比用几倍数据量训练出来的模型效果还好。
## 核心工作

## 相关工作
### IFT的作用
1. IFT阶段任务的多样性很重要；
2. 一些论文论证了：做rlhf时，响应长度和reward之间的强关联性；dpo也会鼓励更长的输出。

### IFT的数据选择
研究者focus创建高质量的ift数据：
1. alpaca创建了52k的数据；
2. alpagasus采用llm来衡量alpaca数据的质量。

data质量经常包含其他的因子：query的复杂度、任务的困难度、语义的多样性；lima依赖社区和人工构造了1k个高质量样本（着重于样本质量和多样性），超过同属性的llm。
## 核心方法
1. 探索ift样本影响长度的重要性、以及长度作为样本筛选方法的有效性；
   - 本文的做法：选择1k个response最长的指令；
   - 训练：
     - model: llama-2-7b
2. 探索1k个响应最长的样本效果能好到什么程度
   - neftune: 在输入的embedding上加入噪声，效果提升7.88%

## 基线
1. alpaca
2. lima
3. self-instruct
## 数据集
## 效果
longest-response效果最好；并且证明了并非因为测试集输出更长，而更被gpt喜欢的消融实验

## 消融实验
## 结论

---

# What Makes Good Data for Alignment? A Comprehensive Study of Automatic Data Selection in Instruction Tuning

## 摘要
IFT阶段，数据工程很重要，如果选择合适，只需要少量数据就行。但是我们仍然缺乏对数据自动选择的有效理解。本文从数据复杂度、多样性、数量三个维度理解数据，提出Deita，用更少的数据执行更好（1/10）。

## 背景
标准的质量对齐包含指令微调和RLHF，先指令微调后再RLHF。不同于传统模型关注样本数量，LLM在预训练阶段获取了所有的知识，而指令微调更多的是将模型的能力对齐到期望的方向，在这个场景下，高质量的数据集更重要（几百到几千）。

## 动机
但是怎么定义好的指令微调数据集，以及怎么系统地使用最少的数据构建有效数据集仍然 unclear。

## 核心工作
本文探索定义指令微调的 "good data"，推动自动指令微调数据集的样本效率提升。先从复杂度、数量、多样性三个角度探索定量分析方法，提出与指令微调效果一致的新 metrics，设计一种简单的策略进行有效的样本选择。

## 相关工作
### 探索样本指标对指令微调的影响
1. 根据一个给定指标选择样本子集
2. 使用该样本训练模型
3. 评估训练后的模型效果

### 训练部分参数
- model: llama-1-13b
- 数据集大小：6k
- 测试数据集：mt-bench
- 测试方法：llm as judge; gpt4
- 训练环境：a100 deepspeed-zero-3 falsh-attn2 max_len: 2048

### 超参数
1. llama-1-13b: batch_size: 128; num_epoch: 6; lr: 1e-5; warm_ratio: 0.03
2. llama-2-13b: batch_size: 128; num_epoch: 6; lr: 2e-5; warm_ratio: 0.1
3. mistral-7b-sft: batch_size: 512; num_epoch: 6; lr: 2e-5; warm_ratio: 0.1
4. mistral-7b-dpo: batch_size: 32; num_epoch: 9; lr: 5e-7; warm_ratio: 0.1

### 对比基线
1. random策略：随机选择
2. 长度：把指令的长度认为是复杂度指标
3. response的ppl
4. 利用gpt4打分
5. 指令节点：利用gpt4分析指令的语法树节点数量
6. 样本tag数量
7. ifd：基于response loss
8. 进化复杂度：针对原始指令-answer对，不断加约束，多次迭代提升复杂度。利用收集到的数据，训练一个llama-1-7b来预测指令-answer的难度。多轮数据的难度=每个step难度的和
9. 进化质量：类似8，衡量样本的回复质量

## 实验结论
进化的方式效果最好；ppl效果竟然很一般

## 核心方法
基于上面的实验结论，提出一种简单的样本选择策略：同时考虑复杂度、质量、多样性。
- score = 复杂度 * 质量得分
- 之后衡量sentence_embed距离小于特定超参的样本

## 基线
1. random
2. lima
3. taglm

## 数据集

## 效果

## 消融实验

## 结论