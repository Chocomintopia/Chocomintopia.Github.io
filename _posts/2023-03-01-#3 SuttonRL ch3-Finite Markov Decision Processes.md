本章介绍有限马尔可夫决策过程（finite MDPs）．

会涉及到 ch2 提到的评估反馈，但现在也会开始考虑问题与环境的联系，也就是在不同情境下去做不同的选择．MDPs 是决策序列的一种经典形式化模型，其中的行动不仅会影响当前的即时 reward，也会通过未来的 reward 影响后续的情况或状态．

<br>

#### 3.1 The Agent–Environment Interface

![finite MDPs](/images/3_finite MDPs.PNG)

- **agent**: 智能体 学习者/决策者
- **environment**: 环境 与 agent 交互的一切外界物体的集合

将 agent 与 environment 的交互看作离散的时间序列，agent 从感知到初始环境 $$S_0$$ 开始，然后决定做一个相应的 action $$A_0$$，environment 相应地发生改变到新的状态 $$S_1$$，并反馈给 agent 一个即时 reward $$R_1$$，然后 agent 又根据状态 $$S_1$$ 做一个 action $$A_1$$，environment 相应改变为 $$S_2$$，并反馈 reward $$R_2$$，这样的交互可以一直下去．

$$
S_0,A_0,S_1,R_1,A_1,S_2,R_2,...,S_{t-1},R_{t-1},S_t,R_t,...,
$$

其中 $$R_t=R(S_{t-1},A_{t-1},S_t)$$ 是 t 时刻的即时 reward．

在 finite MDP 中，$$R_t$$ 和 $$S_t$$ 仅依赖于前一个 state 和 action（$$S_{t-1}$$ 和 $$A_{t-1}$$），对于 $$\forall s',s\in \mathcal{S},r\in \mathcal{R},a\in \mathcal{A}(s)$$，有： 
$$
p(s',r\vert s,a)\doteq Pr\{S_t=s',R_t=r\vert S_{t-1}=s,A_{t-1}=a\}\tag{3.2}
$$
function $$p$$ 定义了 MDP 的 **dynamics**．$$\doteq$$ 意为这是一个定义，而不是根据从前的定义得出的事实．dynamics function $$p : \mathcal{S} \times \mathcal{R} \times \mathcal{S} \times \mathcal{A} \rightarrow [0,1]$$ 是一个普通的 4 arguments - deterministic function，中间的 “$$\vert$$” 来自于条件概率，但此处只是提醒我们 $$p$$ 为每个 s 和 a 的选择 列举了概率分布，即
$$
\sum_{s'\in\mathcal{S}}\sum_{r\in\mathcal{R}}p(s',r|s,a)=1, for\;all\;s\in \mathcal{S},a\in \mathcal{A}(s)
$$

如果一个 state 包含 对于 future 有影响的 过去 agent-environment 交互的 所有方面的信息（“The state must include information about all aspects of the past agent-environment interaction that make a difference for the future”），称其具有 **Markov property**．

3-argument function $$P:\mathcal{S} \times \mathcal{S} \times\mathcal{A} \rightarrow [0,1]$$（state-transition probabilities 状态转移概率）
$$
p(s'|s,a)\doteq\mathrm{Pr}\left\{S_t=s'|S_{t-1}=s,A_{t-1}=a\right\}=\sum_{r\in\mathcal{R}}p(s',r|s,a)
$$

2-argument function $$r:\mathcal{S} \times \mathcal{A} \rightarrow \mathbb{R}$$（the expected rewards for state-action pairs）
$$
r(s,a)\doteq \mathbb{E}\left[R_t|S_{t-1}=s,A_{t-1}=a\right]=\sum_{r\in\mathcal{R}}r\sum_{s'\in\mathcal{S}}p(s',r|s,a)
$$

3-argument function $$r:\mathcal{S} \times \mathcal{A} \times \mathcal{S}\rightarrow \mathbb{R}$$（the expected rewards for state-action-next state）
$$
r(s,a,s')\doteq \mathbb{E}\left[R_t|S_{t-1}=s,A_{t-1}=a,S_t=s'\right]=\sum_{r\in\mathcal{R}}r\frac{p(s',r|s,a)}{p(s'|s,a)}
$$
本书主要常用 (3.2)，其它几个偶尔用．

<br>

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

<br>

#### 3.2 Goals and Rewards

在强化学习中，agent 的 goal 通过 environment 传入的 reward 信号来量化，具体而言，目标即为**最大化 reward 的累积求和**．换一种说法，不同的 reward 决定了不同的目标、不同的学习问题．

举个简单的例子作为对比：

- 让机器人学会走路：在每个 time step 提供与机器人行走距离成正比的 reward，提供正值作为 reward，则会不断「鼓励」机器人多走路
- 让机器人学会逃出迷宫：逃出迷宫前的每一个 time step 都提供 -1 作为 reward，提供负值作为 reward，「促使」机器人找到一条最短路径逃出迷宫

前面提到 agent 的目标就是最大化累积奖励值，而它的行动也很大程度受到我们给定的 rewards 的影响，不恰当地设定 rewards 必然会影响到 agent 的学习效果．所以我们要提供的 rewards ，是要用于**促使** agent 来**达成目标**，而不是根据人的先验知识来**告诉他该怎样去做**．就是不要去人为「干预」他怎样学，只管给他设定目标和一些基本规则，让他多去自由探索．

“The reward signial is your way of communicating to the agent ***what*** you want achieved, not ***how*** you want it achieved.”

比如在下棋时，应该在真正赢下比赛时才给予适当奖励，而不是在吃掉对手某个棋子，亦或占据某个区域时就立即给他奖励．虽然按人的理解，吃掉棋子之类的行动一般都看似是个不错的选择，但在一些关键的步骤，说不定以退为进才是上策，盲目吃子反而陷入陷阱．所以如果按后者那样的策略去学习，agent 最终总会倾向于不顾输赢地去拿下这些眼前利益，而不去长远考虑，失去「大局观」．

之前强调过，rewards 的计算要放在 agent 外界，现在不难明白，原因就在于 agent 的终极目标是要去掌握之前并不能完美掌握的事物，而这正是我们设计强化学习来解决难题的本质．

#### 3.3 Returns

前面说过强化学习的目标就是最大化累积奖励，那么如何用数学语言表达呢？

一般情况下，我们寻求 expected return 的最大化，其中这个 return，定义为 $$G_t$$，是 reward 序列的一些特定函数，return 最简单的一种形式是 rewards 的求和：（T 是 final time step）

$$
G_t\doteq R_{t+1}+R_{t+2}+R_{t+3}+\cdots+R_T
$$

##### Episodic Tasks

上面的方法仅适用于有 final time step 的任务，agent 与 environment 的交互分割成一个个子序列，我们称之为 **episodes**（任意重复性的交互过程）．每一个 episode 结束在一个特殊状态，称之为 **terminal state**，然后复位到一个标准的起始状态．即便你认为 episode 是以不同方式结束的，比如赢得或输掉一场比赛，下一个 episode 的开始也与前一个 episode 的结束方式无关．因此，所有的 episode 都可以被认为是以相同的 terminal state 结束，不同的结果有不同的 reward．有这种 episode 的任务称为称为 **episode task**．在 episode task 中我们常常需要区分 所有 nonterminal state 的集合，定义为 $$\mathcal{S}$$ 和 所有 states 和 terminal state 的集合，定义为 $$S^+$$．

##### Continuing Tasks

许多情况中，agent 和 environment 的交互过程不能被分割成 episodes，而是没有极限地持续下去，称为 continuing tasks．此时上面的 $$G_t$$ 会出现问题，因为 final time step 会是 $$T=\infty$$ ，我们尝试最大化的 return 也很容易变成无穷．下面引入削减系数 $$\gamma$$．

##### Discounted Return

$$
G_t\doteq R_{t+1}+\gamma R_{t+2}+\gamma^2 R_{t+3}+\cdots = \sum_{k=0}^{\infty}\gamma^kR_{t+k+1}
$$

其中 $$\gamma(0\leq \gamma \leq 1)$$ 是 discount rate，要选择能让 discounted return 最大化的 $$A_t$$．

- 若 $$\gamma \rightarrow 0$$，agent 更着重于最大化即时奖励值，也就是说，此时他的目标为学习如何选择 $$A_t$$ 来最大化 $$R_{t+1}$$
- 若 $$\gamma \rightarrow 1$$，返回值会更加强烈地把未来的奖励情况考虑进来，使得 agent 变得更有「远见」

<br>

#### 3.4 Unified Notation for Episodic and Continuing Tasks

在后面的讨论中，我们会对各种任务来统一地来讨论分析，所以需要统一符号．

在 episode tasks 中，我们应该用 $$S_{t,i}$$ 来表示第 i 段的第 t 步（ $$A_{t,i}, R_{t,i}, \pi_{t,i}, T_i$$ 等同理），但是每一段其实本质上意义相近，对于其他段我们可以统一分析，故简写 $$S_t$$ 来统一表示各个 $$S_{t,i}$$，其他几个符号也同理．

此外，我们还需要统一 episode tasks 和 continuing tasks 的形式，他们分别有着有限项的 return 公式和无限项的 return 公式，为了统一公式，我们针对 episode tasks 加入一种特殊的 absorbing state，其特点是状态的交互和转移过程都只在自身进行，且 reward 为 0 ，如下图所示：

![Absorbing State](/images/3_unified.png)

这样 episode tasks 也能表示为无穷项了，只不过原本的 terminal state 之后 reward 为 0，不对公式造成影响．于是我们可以统一地定义：

$$
G_t\doteq \sum_{k=t+1}^{T}\gamma^{k-t-1}R_k
$$

<br>

#### 3.5 Policies and Value Functions

几乎所有的强化学习算法都包含估计 **value function** —— 估计 agent 在一个给定的 state 下有*多好*（或者在一个给定 state 下做出一个给定的 action 有多好）的 states（或者state-action对）的函数．这里面“多好”（how good）的概念是指 future reward 的期望，准确地说是指 return 的期望．当然 agent 的 future reward 期望取决于它将要采取什么 action ．因此，value function 是由特定的行动方式（称之为 **policy**）而决定的．

policy 是一个从 states 到 选择每个可能的 action 的概率 的映射．如果 agent 在时间 t 遵循 policy $$\pi$$ ，那么 $$\pi(a\vert s)$$ 是在 $$S_t=s$$ 时 $$A_t=a$$ 的概率．类似于 $$ p$$，$$\pi$$ 是一个普通的函数，$$\pi(a\vert s)$$ 中间的 “$$\vert$$” 仅仅是提示我们它为每一个 $$s\in \mathcal{S}$$ 确定了一个关于 $$a\in \mathcal{A}(s)$$ 的概率分布．强化学习方法规定了 agent 的 policy 如何因其经验而改变．

**练习 3.11**  如果当前 state 为 $$S_t$$，并且根据随机 policy $$\pi$$ 来选择 action，那么对于 $$\pi$$ 和 4-argument function $$p$$ (3.2)，$$R_{t+1}$$ 的期望是多少？

policy $$\pi$$ 下的 state s 的 value function 表示为 $$v_{\pi}(s)$$，是从 state s 开始并且之后遵循 policy $$\pi$$ 的 expected return．对于 MDPs，可以正式地定义 $$v_{\pi}$$ 为：
$$
v_{\pi}(s)\doteq\mathbb{E}_{\pi}\left[G_t|S_t=s\right]=\mathbb{E}_{\pi}\left[\sum_{k=0}^{\infty}\gamma^kR_{t+k+1}\mid S_t=s\right], \forall s \in \mathcal{S}
$$
其中 $$\mathbb{E}[·]$$ 表示 agent 遵循 policy $$\pi$$ 的随机变量的 expected value，t 是任意 time step．注意，如果有 terminal value，其 value 一直为 0．我们称 function $$v_{\pi}$$ 是 **policy $$\pi$$ 的 state-value function **．















参考：

[sutton RL2020](http://incompleteideas.net/sutton/book/RLbook2020.pdf)

[zhangwpblog RLAI_3](https://www.zhangwp.com/notes/book-reading/RLAI/RLAI_3)

[nndl ch14](https://nndl.github.io/nndl-book.pdf)