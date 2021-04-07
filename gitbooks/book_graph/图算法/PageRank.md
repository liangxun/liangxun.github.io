# PageRank

pagerank的基本思想是每个节点的影响力是又他的邻居决定的

在社交网络中，我们可以通过一个人认识的人的重要性来确定他的重要性

定义了结点的pagerank值
$$
R(u) = c \sum_{v \in B_u} \frac{R(v)}{N_v}
$$
其中$v \in B_u$表示有一条边由节点$v$指向$u$. 即$v \rightarrow u$. 注意这里除以了$N_v$，其实就是

pagerank原论文中的图介绍地非常清楚

<img src="../../../BUPT/Notes/resource/pagerank.jpg" alt="pagerank" style="zoom:33%;" />

可以从随机游走的角度来解释pagerank。有一定的概率随机跳转

pagerank的理论基础：不可约且非周期的有限状态马尔可夫链，有唯一平稳分布存在



#### 改进的pagerank

《统计学习方法2》中把这个解释成朝原来的模型中增加平滑项，那种解释比较好理解。

实际上使用的pagerank是改进型：
$$
R'(u) = c \sum_{v \in B_u} \frac{R'(v)}{N_v} + cE(u)
$$

在加入平滑项之后，能够保证组合转移矩阵是非周期




最开始的pagerank值可以设置成一样的，通过训练会收敛到平衡状态



在[dgl的pagerank](https://docs.dgl.ai/tutorials/basics/3_pagerank.html)实现中公式是:
$$
PR(u) = \frac{1-d}{N} + d \times \sum_{v \in N(u)} \frac{PR(v)}{D(v)}  
$$
$d$通常设置为固定值0.85，$N$是图中结点的数目，通常每个结点的PR值初始化为$\frac{1}{N}$



参考：

http://blog.codinglabs.org/articles/intro-to-pagerank.html

[http://chennanxu.github.io/2017/03/19/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3PageRank/](http://chennanxu.github.io/2017/03/19/深入理解PageRank/)



**问题：如何从矩阵的角度解释和理解pagerank？**

简单来说就是转移矩阵乘状态向量

$$
R_{t+1} = M R_t
$$
其中M是转移概率矩阵，不要当成邻居矩阵，转移概率矩阵就是邻接矩阵除以节点的度做归一化处理

改进的定义用矩阵表示则是
$$
R_{t+1} = dMR_t + \frac{1-d}{n}
$$

**问题：具体的解法**

1. 根据定义迭代计算，大多数其实就是这个东西。注意这里每次乘的是真实的概率转移矩阵$M$

2. 幂法。幂法本身是用于近似计算矩阵的主特征值和主特征向量。而这里存在

   有一个定理：转移矩阵A对应的pagerank向量其实就是矩阵$A$的主特征向量，主特征值是1

   $A=dM+\frac{1-d}{n}E$

   所以说可以用幂法算$A$的主特征向量，然后把这个主特征向量归一化成概率分布就成pagerank向量



其实本质都是迭代地运行 向量=矩阵*向量



> 问题：为什么主特征值是1？
>
> 马尔可夫转移矩阵最大特征值等于1， 这里转移矩阵就是一个马儿可夫转移矩阵，它的主特征值
>
> 很容易证明的，随机矩阵肯定有特征值1[随机矩阵及其特征值](https://bitjoy.net/2016/08/23/the-eigenvalue-of-stochastic-matrix/)

$M$和$A$行的和是1，所以他们都是随机矩阵



> 问题：既然$A \vec1 = 1 \vec 1$成立, 那岂不是主特征向量就是$\vec 1$？
>
> 