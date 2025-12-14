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

# What Makes Good Data for Alignment? A Comprehensive Study of Automatic Data Selection in Instruction Tuning

## 摘要
IFT 阶段，数据工程很重要，如果选择合适，只需要少量数据就行。但是我们仍然缺乏对数据自动选择的有效理解。本文从数据复杂度、多样性、数量三个维度理解数据，提出 Deita，用更少的数据执行更好（1/10）。

## 背景
标准的质量对齐包含指令微调和 RLHF，先指令微调后再 RLHF。不同于传统模型关注样本数量，LLM 在预训练阶段获取了所有的知识，而指令微调更多的是将模型的能力对齐到期望的方向，在这个场景下，高质量的数据集更重要（几百到几千）。

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

#### 对比基线
1. random策略：随机选择
2. 长度：把指令的长度认为是复杂度指标
3. response 的ppl
4. 利用gpt4打分
5. 指令节点：利用gpt4分析指令的语法树节点数量
6. 样本tag数量
7. ifd：基于response loss
8. 进化复杂度：针对原始指令-answer对，不断加约束，多次迭代提升复杂度。利用收集到的数据，训练一个llama-1-7b 来预测指令-answer的难度。多轮数据的难度=每个step难度的和
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