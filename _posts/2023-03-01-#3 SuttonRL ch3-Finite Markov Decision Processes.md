本章介绍有限马尔可夫决策过程（finite MDPs）．

会涉及到 ch2 提到的评估反馈，但现在也会开始考虑问题与环境的联系，也就是在不同情境下去做不同的选择．MDPs 是决策序列的一种经典形式化模型，其中的行动不仅会影响当前的即时 reward，也会通过未来的 reward 影响后续的情况或状态．



#### 3.1 The Agent–Environment Interface

![finite MDPs](/images/3_finite MDPs.PNG)

- **agent**: 智能体 学习者/决策者
- **environment**: 环境 与 agent 交互的一切外界物体的集合

将 agent 与 environment 的交互看作离散的时间序列，agent 从感知到初始环境 $$S_0$$ 开始，然后决定做一个相应的 action $$A_0$$，environment 相应地发生改变到新的状态 $$S_1$$，并反馈给 agent 一个即时 reward $$R_1$$，然后 agent 又根据状态 $$S_1$$ 做一个 action $$A_1$$，environment 相应改变为 $$S_2$$，并反馈 reward $$R_2$$，这样的交互可以一直下去．
$$
S_0,A_0,S_1,R_1,A_1,S_2,R_2,...,S_{t-1},R_{t-1},S_t,R_t,...,
$$
其中 $$R_t=R(S_{t-1},A_{t-1},S_t)$$ 是 t 时刻的即时 reward．

那么如何划清 agent 和 environment 的界限？

不能被 agent 任意改变的事物，均认为其属于 environment．agent 往往能够得知很多外界的信息，甚至能知道 reward 关于其 action 和 state 的具体函数，但我们还是得把 reward 的计算过程放在 agent 外界，而不能让他自己进行计算．这是因为，正是这个 reward 机制定义了 agent 所要学习去处理的任务，所以这个任务必须是超出 agent 控制能力的，反之则本末倒置了，如果 agent 能自我计算 / 改变 reward ，那他会按照自己的一些「理解」去学习问题，而没有面对问题的「客观本质」去学习．

所以，agent 与 environment 的界限，代表着 学习者 / 决策体 的绝对控制权，而不是其掌握的 知识 / 信息．而一旦确定好三个关键因素 state、action、reward，就意味着这个界限已被确定下来．

任何 goal-directed 的学习问题，都可以简化为三个信号在 agent 和 environment 间前后传递的模型：

- the actions: “the choices made by the agent”
- the states: “the basis on which the choices are made”
- the rewards: “the agent’s goal”

##### Example: Recycling Robot

举一个清洁机器人的例子来展示 actions、states、rewards 在具体问题中是什么．

机器人的主要目标是持续清洁垃圾，但要注意电量不被用完，所以有时需要回去充电：

*actions:*

- 活动起来去搜寻垃圾（耗电）
- 保持在原地等人来捡垃圾（不耗电）
- 回去充电

*states:*

- 电池状态

*rewards:*

- 如果清洁了垃圾，反馈一个正值 reward
- 如果耗尽电量而没能及时返回充电，反馈一个较大负值作为惩罚
- 其余情况反馈 0 值 reward 



#### 3.2 Goals and Rewards



参考：

[sutton RL2020](http://incompleteideas.net/sutton/book/RLbook2020.pdf)

[zhangwpblog RLAI_3](https://www.zhangwp.com/notes/book-reading/RLAI/RLAI_3)

[nndl ch14](https://nndl.github.io/nndl-book.pdf)