---
layout: post
title:  "混合推荐论文摘要"
subtitle:   ""  
date:       2016-04-26
author:     "allanruin"
header-img: "img/posthead/post-bg-js-module.jpg"
<!-- header-mask: 0.5 -->
<!-- catalog: true -->
tags:
    - 推荐系统
    - 论文

---

# 使用PSO进行优化的论文

《Enhancing scalability and accuracy of recommendation systems using unsupervised learning and particle swarm optimization》
这篇文章通过利用粒子群对alpha参数进行优化，作者说效果比固定的alpha的要好，还说性能相对遗传算法的要好。讲道理，这篇论文的遣词造句看着累。

>It was established experimentally that when the data is highly sparse, globally similar neighbours provide better prediction, whereas in case of low sparsity, predictions from local neighbours are superior

作者提到业界一些相似度度量方式有：

1. Proximity-Impact-Popularity (PIP) measure, [3] （应对cold-starting problem）
2. UNION similarity [45]
3. random walk counting [18]
4. user-class similarity [49],
5. iterative message passing procedure

### PIP

$$PIP(r_1,r_2) = Proximity(r_1,r_2) · Impact(r_1,r_2) · Popularity(r_1,r_2)$$
![](/img/in-post/QQ截图20160427113047.png)
PIP考虑了三个因素，`Proximit`表征了两个用户观点是否一致（都是正面还是都是反面或者以一个正面评价，另一个用户负面评价）。对于评价情感不一致的给与较大的罚项。`Impact`：可以理解为冲击，如果用户给出的分数非常极端，如满分，则我们可以认为这个评分强烈的表达了用户偏好。
`Popularity`：如果用户的评分跟大众评分较接近，则其实这个评分的信息量比较小。

`iterative-clustered collaborative filtering` (ICCF), spectral clustering is iteratively utilized in both user-based and item-based collaborative filtering to predict the unknown ratings. 然而这种方法对所有用户和物品都有着相同的权值。


该论文作者在用户相似度上，加入了对不同特征的不同权重，并用粒子群算法进行权重的优化。
![](/img/in-post/QQ截图20160427004048.png)
这里的想法就是，比如某个男性观众，更喜欢被推荐其他男性用户认同的电影，那么比较用户相似度的时候，性别这个特征就应当被赋予更高的权重。不过我不太理解

>rofile values are normalised to ensure they lie between 0 and 1.

系统的结构大概这样：![](/img/in-post/QQ截图20160427004453.png)

粒子群优化方法的思想是，记录所有粒子每轮中所在位置fitness最好的位置为全局追最佳`gbest`,每个粒子自己在历史位置中fitness最好的位置称为个人最佳或者局部最佳`pbest`。位置更新规则就是新速度由原来的速度、全局最佳位置、局部最佳位置来确定。

$$v_i = w*v_i + c_1*r_1*(x_{pbest,i}-x_i) + c_2*r_2*(x_{gbest}-x_i) $$

$$ if(|v_i|>v_{max})  v_i = (v_{max}/v_i|)*v_i $$

$$  x_i = x_i + v_i $$

![](/img/in-post/QQ截图20160427010454.png)

# 较新的论文
## Clustering Algorithms in Hybrid Recommender System on MovieLens Data

## From Word Embeddings to Item Recommendation
文章里直接把`word embeddings` 应用到非文本数据上（过去的check-in记录），进行推荐。之前的利用word embedings的只是利用word2vec把文本数据转为vector进行处理。

skip-gram，continuous bag of words

While the CBOW technique uses the words around the current word to predict the current word, the skip-gram technique does the vice-versa, such that it uses the current word to predict the words around the current word

### NDCG
`Ndcg (Normalized discounted cumulative gain)` metric 归一化折扣增益值
DCG方法的基本思想是：

1. 每条结果的相关性分等级来衡量
2. 考虑结果所在的位置，位置越靠前的则重要程度越高
3. 等级高（即好结果）的结果位置越靠前则值应该越高，否则给予惩罚

我们首先来看第一条：相关性分级。这里比计算Precision时简单统计“准确”或“不准确”要更为精细。我们可以将结果细分为多个等级。比如常用的3级：Good（好）、Fair（一般）、Bad（差）。对应的分值rel为：Good:3 / Fair:2 / Bad:1 。一些更为细致的评估使用5级分类法：Very Good（明显好）、Good（好）、Fair（一般）、Bad（差）、Very Bad（明显差），可以将对应分值rel设置为：Very Good:2 / Good:1 / Fair:0 / Bad:-1 / Very Bad: -2
![](/img/in-post/DCG_original_formula.jpg)
![](/img/in-post/DCG_smooth_formula.jpg)
第二个公式是加入平滑的结果。


为了便于不同类型的query结果之间横向比较，以DCG为基础，一些评价系统还对DCG进行了归一，这些方法统称为nDCG（即 normalize DCG）。最常用的计算方法是通过除以每一个查询的理想值iDCG（ideal DCG）来进行归一，公式为：
![](http://www.infoq.com/resource/articles/cyw-evaluate-seachengine-result-quality/zh/resources/image16.jpg)
求nDCG需要标定出理想情况的iDCG，实际操作的时候是异常困难的，因为每个人对“最好的结果”理解往往各不相同，从海量数据里选出最优结果是很困难的任务，但是比较两组结果哪个更好通常更容易，所以实践应用中，通常选择结果对比的方法进行评估。

### Hitrate
Hitrate评估了至少给出了一个真实推荐的用户比例。

# 提到的论文

《Particle Swarm Optimization Recommender System》这个也是使用PSO进行优化的。

each particle or solution contains a position, velocity and acceleration。Each location has a fitness value according to how good it is at satisfying the
objective


# 生词
lacunae

# 资料
http://www.infoq.com/cn/articles/cyw-evaluate-seachengine-result-quality