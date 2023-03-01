本章主要针对 **nonassociative** 问题，即无需考虑每一步行动之间的影响，以及环境对行动的影响，实际上是一种很理想化的问题，实用性不大，但作为强化学习入门还不错．

强化学习和其它机器学习方法最大的区别，“**evaluates** the actions taken rather than **instructs** by giving correct actions”．

- 评价性反馈（evaluative feedback）：知道每一步 action 的好坏程度，但不知道这个 action 是否是最好 / 最差
- 指导性反馈（instructive feedback）：直接得知最优 action



#### 2.1 A k-armed Bandit Problem

##### Background

k-armed-bandit：

- 每次在 k 个选项中做出一个选择，称之为一个 **action**
- 每次根据玩家的 action 反馈一个 **reward**，每种 action 对应的奖励值服从一个固定的概率分布（这个概率分布是我们从背后分析问题，也就是从上帝视角才能得知的，真正的玩家一开始根本不知道奖励值服从什么规律或者是否有规律，他需要通过 “学习” 来找到这一规律）
- 玩家的目标在于使收获的奖励的累积值最大化，“to maximize the expected total reward over some time period, for example, over 1000 action selections, or *time steps*”

##### Representation of the problem

$$
q_*(a)\doteq \mathbb{E}[R_t\vert A_t=a]
$$

- $$A_t:$$ time step t 时作出的 action 
- $$R_t:$$ time step t 后得到的 reward
- value: expected or mean reward given that that action is selected
- $$q_*(a):$$ 任意一个 action a 的 value，即 the expected reward given that a is selected

如果我们真的知道这台老虎机的 reward 规律，即知道每个 action a 的 $$q_*(a)$$ ，那么只需要每次都选择 value 最大的 action 就能累计值最大化；但我们其实不知道 $$q_*(a)$$ ，不过可以估计一个 time step t 时的 $$Q_t(a)$$，然后希望 $$Q_t(a)$$ 接近于 $$q_*(a)$$．

如何估计这个 $$Q_t(a)$$ 呢？这正是后文要长篇大论的东西，暂且不提，但目前我们先假设已经有了一个评估体系，那么该如何根据评价型反馈来采取行动呢？

维护 action value 的估计值，每个 time step 都选择 estimated value 最大的 action :: **greedy**

选择estimated value最大的action时，我们说 你在利用对于 action value的了解 :: **exploit**

如果选择一个 nongreedy action，我们说 你在探索 :: **explore**

*balance exploration and exploration*



#### 2.2 Action-value Methods

##### Sample-Average

前面提到需要建设评估体系，本次就介绍一个最简单基础的方法：

一个自然的想法是将过去得到的 reward 取均值作为这一次对该 action 的评估

$$
Q_t(a)\doteq \frac{sum\; of\;rewards\;when\;a\;taken\;prior\;to\;t}{number\;of\;times\;a\;taken\;prior\;to\;t} = \frac{\sum _{i=1} ^{t-1}{R_i\cdot\mathbb{1}_{A_i=a}}}{\sum _{i=1} ^{t-1}{\mathbb{1}_{A_i=a}}}
$$

*$$\mathbb{1}_{predicate}$$ is 1 if predicate is true and 0 if it is not*

分母 $$\displaystyle \sum_{i=1}^{t-1}\textbf{1}_{A_{i} = a} = 0$$，该分式无意义，可以给 $$Q_t(a)$$ 设定一个默认值，比如0；

分母 $$\displaystyle \sum_{i=1}^{t-1}\textbf{1}_{A_{i} = a}\rightarrow \infty$$ ，根据大数定律，$$Q_t(a)$$ 收敛于 $$q_*(a)$$．

##### greedy action

$$
A_t \doteq \mathop{\arg\max}\limits_aQ_t(a)
$$

纯粹的 greedy 会陷入局部最优，很难让每个 action 都满足 $$\displaystyle \sum_{i=1}^{t-1}\textbf{1}_{A_{i} = a}\rightarrow \infty$$，此时需要一些 exploration．

##### ε-greedy action

> *ε-greedy action :* 以 1-ε 的概率采取贪心行动，ε 概率随机选择一个行动 $$a$$

能保证 $$Q_t(a)$$ 收敛于 $$q_*(a)$$，选择最优 action 的概率收敛于一个略大于 1-ε 的接近确定的值．



#### 2.4 Incremental Implementation

##### Optimization

目前只集中于关注某个具体的 action a，其它的类比即可．

令 $$R_i$$ 表示第 i 次选到 action a 时的 reward，$$Q_n$$ 表示在 action a 被选择了 n-1 次之后对于这个 action a 的 value 的估计值，则由 [Sample-Average](#sample-average) 有

$$
Q_n\doteq \dfrac{R_1+R_2+\cdots + R_{n-1}}{n-1}
$$
我们需要维护所有 reward 的记录，很占用内存，下面进行简化：

$$
\begin{aligned}
Q_{n+1} & = \dfrac{1}{n}\sum_{i=1}^{n}R_i\\
& = \dfrac{1}{n}\left(R_n+\sum_{i=1}^{n-1}R_i\right)\\
& = \dfrac{1}{n}\left(R_n+(n-1)\dfrac{1}{n-1}\sum_{i=1}^{n-1}R_i\right)\\
& = \dfrac{1}{n}\left(R_n+(n-1)Q_n\right)\\
& = \dfrac{1}{n}\left(R_n+nQ_n-Q_n\right)\\
& = Q_n + \dfrac{1}{n}\left[R_n-Q_n\right],
\end{aligned}
$$

$$
NewEstimate \leftarrow OldEstimate + StepSize[Target - OldEstimate]
$$

[Target - OldEstimate] 是估计的误差 error，上面 [imcremental method](#24-incremental-implementation)的 stepsize，在每个 time step 都发生改变．对于 action a 的第 n 次 reward，此方法用的 stepsize 参数是 1/n，本书我们把 stepsize 参数设定为 $$\alpha$$，或者 $$\alpha_t(a)$$．

##### pseudocode

> 初始化，for a = 1 to k:
>
> $$\qquad Q(a)\leftarrow 0$$
>
> $$\qquad N(a)\leftarrow 0$$
>
> Repeat forever:
>
> $$\qquad A \leftarrow \begin{cases} \mathop{\arg\max}\limits_{a}Q(a) & \text{with probability} \ 1-\epsilon\\ \text{a random action} & \text{with probability} \ \epsilon \end{cases}$$
>
> $$\qquad R\leftarrow bandit(A)$$（函数 bandit(a) 是输入一个 action，返回对应的 reward）
>
> $$\qquad N(A)\leftarrow N(A)+1$$
>
> $$\qquad Q(A)\leftarrow Q(A) + \dfrac{1}{N(A)}[R-Q(A)]$$



#### 2.5 Tracking a Nonstationary Problem

前面都是基于 reward 符合固定概率分布，但现实中的问题并非如此理想化，该如何解决？

这种情况下需要给最近的 reward 更高的权重．一个常见的做法是，将前面增量式中的参数 stepsize 设为一个**常量** $$\alpha \in (0,1]$$，则有
$$
Q_{n+1}\doteq Q_n + \alpha[R_n-Q_n] (\alpha \in (0,1])
$$
（再次强调这几节都是只针对某一个特定的 action）

对上面式子进行整理：
$$
\begin{aligned}
Q_{n+1} &\doteq Q_{n} + \alpha[R_{n} - Q_{n}]
\\ &= \alpha R_{n} + (1 - \alpha)Q_{n}
\\ &= \alpha R_{n} + (1 - \alpha)[\alpha R_{n-1} + (1-\alpha)Q_{n-1}]
\\ &= \alpha R_{n} + (1 -\alpha)\alpha R_{n-1} + (1 - \alpha)^{2}Q_{n-1}
\\ &= \alpha R_{n} + (1 - \alpha)\alpha R_{n-1} + (1-\alpha)^{2}\alpha R_{n-2} +...
\\ &+(1-\alpha)^{n-1}\alpha R_{1} + (1-\alpha)^{n}Q_{1}
\\ &= (1-\alpha)^{n}Q_{1}+\sum^{n}_{i=1}(1-\alpha)^{n-i}\alpha R_{i}
\end{aligned}
$$
$$\displaystyle (1-\alpha)^{n}+\sum^{n}_{i=1}\alpha(1-\alpha)^{n-i}=1$$，因此这是一个加权平均式，称为 指数近因加权平均（Exponential Recency-weighted Average）．i 很大时，$$R_i$$ 在式子中的影响占比才更大，符合将学习重心放在近期 reward 的要求．



#### 2.6 Optimistic Initial Values

这一节简单研究了以下初始预估值对模型学习效果的影响．
$$
Q_{n+1} = (1-\alpha)^{n}Q_{1}+\sum^{n}_{i=1}(1-\alpha)^{n-i}\alpha R_{i}
$$
前面讨论的所有方法，对于每个 action 而言，评估体系显然都会一定程度上受到初始值 $$Q_1$$ 的影响，统计学语境中，称为被初值*偏置（bais）*了．

将初始值调高，有着鼓励模型在早期更多地进行探索的作用．以 greedy 策略为例，一个很高的初始预期值（称为**乐观初值**），会诱使模型去选择这个 action ，然而事实上 reward 要比估计值差很多，误差值 $[R_n - Q_n]$ 会是一个较大的负数，导致模型对这个 action “失望”，评价降低，下一次，模型就会去主动尝试其他 action ．

乐观初值仅适用于固定概率分布的问题，它只会在早期 explore，后期仍以 exploit为主，然而 nonstationary 问题下必须时刻探索最近的 reward，乐观初值不再适用．



#### 2.7 Upper-Confidence-Bound Action Selection(UCB)

前面的 ε-greedy 在探索时，每个 action 都是被等概率选择的，但现实中可能会有某个 action 一直返回一个低 reward，潜力低下，需要调低这个不好的 action 的概率，而尽可能多地探索潜力更高的 action．
$$
A_{t} \doteq \mathop{\arg\max}_{a}\left[Q_{t}(a) + c\sqrt{\frac{\ln{t}}{N_{t}(a)}}\right]
$$
UCB 算法就是采取满足上式的 action $$A_t$$ ，算法的核心在于新加入的 $$c\sqrt{\dfrac{\ln t}{N_t(a)}}$$，以适当地提高更加不确定、有潜力的 action 被探索的概率．

 - $$c\sqrt{\dfrac{\ln t}{N_t(a)}}$$ ：对于估值的不确定性．更广义地讲，其意义为方差．
 - $$c$$ ：控制了探索的程度，决定了置信度．
 - $$N_t(a)$$ ：第 t 步之前 action a 被选中的次数．$$N_t(a)$$ 如果增加，会给公式中的此项带来降低的影响效果．
 - $$\ln t$$ ：时间必然会增加，故此项也是一直在保持增加的．但它的影响效果与 action a 的选择状态密切相关．如果 action a 不被选择， $$\ln t$$ 增加但 $$N_t(a)$$ 不变，故此项变大，不确定性增加；反之，$$N_{t+1}(a) = N_t(a) + 1$$ ，虽然 $$\ln t$$ 增加但其增速不如 $$N_t(a)$$ ，所以此项整体变小，不确定性降低．



2.8 2.9 略







参考：
[sutton RL2020](http://incompleteideas.net/sutton/book/RLbook2020.pdf)
[zhangwpblog RLAI_2](https://www.zhangwp.com/notes/book-reading/RLAI/RLAI_2)

