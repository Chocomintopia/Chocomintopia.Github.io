本章主要针对 **nonassociative** 问题，即无需考虑每一步行动之间的影响，以及环境对行动的影响，实际上是一种很理想化的问题，实用性不大，但作为强化学习入门还不错。

强化学习和其它机器学习方法最大的区别，“**evaluates** the actions taken rather than **instructs** by giving correct actions”

- 评价性反馈（evaluative feedback）：知道每一步 action 的好坏程度，但不知道这个 action 是否是最好 / 最差
- 指导性反馈（instructive feedback）：直接得知最优 action

#### 2.1 A k-armed Bandit Problem

##### Background

k-armed-bandit：

- 每次在 k 个选项中做出一个选择，称之为一个 **action**
- 每次根据玩家的 action 反馈一个 **reward**，每种 action 对应的奖励值服从一个固定的概率分布（这个概率分布是我们从背后分析问题，也就是从上帝视角才能得知的，真正的玩家一开始根本不知道奖励值服从什么规律或者是否有规律，他需要通过 “学习” 来找到这一规律）
- 玩家的目标在于使收获的奖励的**累积值**最大化，“to maximize the expected total reward over some time period, for example, over 1000 action selections, or *time steps*”

##### Representation of the problem

$$
q_*(a)\doteq \mathbb{E}[R_t\vert A_t=a]
$$

- $$A_t:$$ time step t 时作出的 action 
- $$R_t:$$ time step t 后得到的 reward
- value: expected or mean reward given that that action is selected
- $$q_*(a):$$ 任意一个 action a 的 value，即 the expected reward given that a is selected

如果我们真的知道这台老虎机的 reward 规律，即知道每个 action a 的 $$q_*(a)$$ ，那么只需要每次都选择 value 最大的 action 就能累计值最大化；但我们其实不知道 $$q_*(a)$$ ，不过可以估计一个 time step t 时的 $$Q_t(a)$$，然后希望 $$Q_t(a)$$ 接近于 $$q_*(a)$$．

如何估计这个 $$Q_t(a)$$ 呢？这正是后文要长篇大论的东西，暂且不提，但目前我们先假设已经有了一个评估体系，那么该如何根据**评价型反馈**来采取行动呢？

维护 action value 的估计值，每个 time step 都选择 estimated value 最大的 action -> **greedy**

选择estimated value最大的action时，我们说 你在**利用**对于 action value的了解 -> **exploit**

如果选择一个 nongreedy action，我们说 你在**探索** -> **explore**

balance exploration and exploration



参考：
[Sutton RL2020](http://incompleteideas.net/sutton/book/RLbook2020.pdf)
[ZHANGWP-RLAI_2](https://www.zhangwp.com/notes/book-reading/RLAI/RLAI_2)

