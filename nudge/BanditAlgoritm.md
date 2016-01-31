# Multi-armed Bandit Algorithm

## 용어

### Reward

A quantitative measure of success. In business, the ultimate rewards are profits, but we can often treat simpler metrics like click-through rates for ads or sign-up rates for new users as rewards. What matters is that (A) there is a clear quantitative scale and (B) it’s better to have more reward than less reward.

정량적으로 측정 가능한 포상, 적게 받는 것보다 많이 받는 것이 좋은 어떤 것

### Arm

What options are available to us? What actions can we take? In this book, we’ll refer to the options available to us as arms. The reasons for this naming convention will be easier to understand after we’ve discuss a little bit of the history of the Multiarmed Bandit Problem.

취할 수 있는 옵션

### Bandit

A bandit is a collection of arms. When you have many options available to you, we call that collection of options a Multiarmed Bandit. A Multiarmed Bandit is a mathematical model you can use to reason about how to make decisions when you have many actions you can take and imperfect information about the rewards you would receive after taking those actions. The algorithms presented in this book are ways of trying to solve the problem of deciding which arms to pull when. We refer to the problem of choosing arms to pull as the Multiarmed Bandit Problem.

옵션의 모음. 옵션이 많을 경우 멀티 암드 밴딧이라고 한다.
Multiarmed Bandit은 여러 조치를 취해볼 순 있지만, 그 조치에 뒤따르는 reward에 대한 정보가 불확실할 때 어떻게 결정을 내리는지를 고민하는데 사용되는 수학적 모델 

### Play/Trial

When you deal with a bandit, it’s assumed that you get to pull on each arm multiple times. Each chance you have to pull on an arm will be called a play or, more often, a trial. The term “play” helps to invoke the notion of gambling that inspires the term “arm”, but the term trial is quite commonly used.

밴딧을 다룰 때는 각 arm을 여러번 당길 수 있다. 한 번 당기는 것을 play 또는 trial이라 한다.

### Horizon

How many trials will you have before the game is finished? The number of trials left is called the horizon. If the horizon is short, you will often use a different strategy than you would use if the horizon were long, because having many chances to play each arm means that you can take greater risks while still having time to recover if anything goes wrong.

arm을 당길 수 있는 잔여 회수. horizon이 많으면 더 위험을 더 감수할 수 있으므로 horizon에 따라 다른 전략을 취하게 된다.


### Exploitation

An algorithm for solving the Multiarmed Bandit Problem exploits if it plays the arm with the highest estimated value based on previous plays.

앞서 수행한 탐색을 토대로 이익을 쓸어담기

### Exploration

An algorithm for solving the Multiarmed Bandit Problem explores if it plays any arm that does not have the highest estimated value based on previous plays. In other words, exploration occurs whenever exploitation does not.

앞선 play에서의 결과를 토대로 할 때 가장 많은 이익을 주지 않는 arm을 당기는 것. exploitation이 아니면 exploration이다. 

### Explore/Exploit Dilemma

The observation that any learning system must strike a compromise between its impulse to explore and its impulse to exploit. The dilemma has no exact solution, but the algorithms described in this book provide useful strategies for resolving the conflicting goals of exploration and exploitation.

정보의 확실성을 높이기 위해 exploration을 많이 하면 이익을 쓸어담는 exploitation 기회가 줄어들고,
이익을 쓸어담는 기회를 늘리기 위해 exploitation을 많이 하면 exploration이 줄어들어 정보의 정확도가 줄어든다.  

### Annealing 

An algorithm for solving the Multiarmed Bandit Problem anneals if it explores less
over time.

시간이 지남에 따라 explore를 줄여가는 것을 annealing 담금질 이라 한다.

### Temperature

A parameter that can be adjusted to increase the amount of exploration in the Softmax algorithm for solving the Multiarmed Bandit Problem. If you decrease the temperature parameter over time, this causes the algorithm to anneal.

softmax 알고리듬에서 exploration의 양을 조절하는데 사용되는 파라미터, temperature를 지속적으로 줄이면 알고리듬은 점점 annealing 된다.


### Streaming Algorithms

An algorithm is a streaming algorithm if it can process data one piece at a time. This is the opposite of batch processing algorithms that need access to all of the data in order to do anything with it.

배치 알고리듬과 다르게 한 번에 하나의 데이터만 처리하는 알고리듬

### Online Learning

An algorithm is an online learning algorithm if it can not only process data one piece at a time, but can also provide provisional results of its analysis after each piece of data is seen.

한 번에 하나의 데이터를 처리하는 것 뿐아니라, 각 데이터의 처리 후 예상 결과를 제공하는 알고리듬

### Active Learning

An algorithm is an active learning algorithm if it can decide which pieces of data it wants to see next in order to learn most effectively. Most traditional machine learning algorithm are not active: they passively accept the data we feed them and do not tell us what data we should collect next.

가장 최적화 된 습득을 위해 다음에 어떤 데이터를 득하는 것이 좋은지 결정할 수 있는 알고리듬.
주어진 데이터를 읽는 일반적인 머신 러닝은 active learning이 아니다.


### Bernoulli

A Bernoulli system outputs a  1 with probability  p and a  0 with probability  1 – p

가능성이 p이면 1을 출력 가능성이 1-p 이면 0을 출력하는 시스템 