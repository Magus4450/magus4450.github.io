---
title: "RL Notes: Chapter 2.2"
date: 2024-11-29
categories: [Reinforcement Learning, ML Notes]
tags: [reinforcement learning, machine learning, markov-decision-processes]
math: true
---

# Markov Decision Processes, Dynamic Programming, and Monte Carlo Methods

- The foundation of RL is based on three topics
	- **Markov Decision Processes (MDPs)**
	- **Dynamic Programming**
	- **Monte Carlo methods**

## Markov Decision Processes (MDP)

- MDP is a mathematical formulation of the foundational concepts of RL
- *agent* receives a limited number of *observations* from the *environment* which represents the current *state* of the system.
- Most of the time, *agents* have occluded view of the world.
- Digital agents perform actions at discrete time, a.k.a. time steps.
- At each time step $t$, the *agent* receives a new state $s$. Based on the state, the *agent* suggests an action, $a$, and one time step later receives the reward, $r$, and a new set of states, $s'$

![Feed-back loop of MDP](assets/img/feedback-loop-mdp.png)

- The states and the rewards are random variables because most environment are stochastic. For example, an agent can choose to turn the steering wheel but if the road has ice in it, it may not turn.
- To cater for this, the concept of a *transition model* is used which is also called *dynamics* of the MDP.
- The probability if then defined as

$$ p(s',r|s,a) $$

### Inventory Control

- Defining a problem in RL terms is a common issue for data scientists.
- Inventory control in a good example to start because it is simple and useful.
- Imagine you have a shop that only sells one product. Each day customers but your product and you have to restock. If you cannot, you can't sell more projects and make money. You also rent you shop and it coss money. You also cannot stock millions because you dont have the space.
- Let $s$ be the number of items on stock on a particular day. Also lets assume three possible states $s \in \{0, 1, 2\}$.
- The action are $a \in \{restock, none\}$. Lets say if you take the action $a = restock$ when in state $s=1$, you will have $s=2$ the next day.
- One thing missing from the equation above in the transition probability: what is the probability of changing the state. In our case, it is the probability of a single sale during a day, $p(sale)$. Consider it to be $0.7$. For simiplicity, lets assume it is a Bernoulli distribution, so the probability of no sale is $1 - p(sale) = 0.3$.
- Final thing we needs is reward, $r$. Sales are only possible if there is stock, so we can define rewards as 
$$ r = \begin{cases}
1 & \text{if } s > 0 \text{ and a sale} \\
1 & \text{if } a = restock \text{ and a sale} \\
0 & \text{otherwise}
\end{cases}
$$

- Next we need to define all the possible state transitions in the system, and we can do this in three different ways:

**Transition table**

![MDP Transition Table](assets/img/mdp_transition_table.png)

**Transition graph**

![MDP Transition graph](assets/img/mdp_transition_graph.png)

**Transition matrix**

![MDP Transition matrix](assets/img/mdp_transition_matrix.png)

### Inventory Control Simulation

- Previous section presented all the ingredients to build a simulation but what about the agent? When should you restock?
- We have a hard rule that we cannot restock when we have full stock.
- We compare three different policies or strategies below:
	- **Keep buying**: restock every time you can
	- **Upon Zero**: restock only when you have no stock
	- **Random**: restock randomly with a probability of 0.5

![Inventory Control Simulation](assets/img/inv_control_sim.png)

- We can see that the **Keep buying** policy is the best one, followed by **Upon Zero** and finally **Random**.
- But the important thing to consider here is that the **Keep buying** policy here is wasteful whereas the **Upon Zero** policy loses sales.
- We've learned how to derive your first set of policies but are they optimal? How can we find the best policy?

## Policy and Value Functions
- Policy is like a strategy: mapping states to potential actions.
- How you do that mapping is the major difference between large number of RL algorithms.

### Discounted Rewards
- At each time step, agents might or not receive a reward. In general, the challenge is to maximize the total reward over time.
- The *return*, $G$, is the total reward from this current step up the final step.
$$ G \overset{\cdot}{=} r + r^{'} + . . . + r_T $$
- $\overset{\cdot}{=}$ can be read as "I define G to be"

- This equation sums all the future rewards until final time step, $T$. The final state is called a *terminal state* and only occures in *episodic tasks*. (One example of episodic task is playing a game, where the game ends when you win or lose.)
- To mitigate the problem of infinite rewards, we can use a *discount factor*, $\gamma$, which is a number between 0 and 1. This means that the future rewards are less important than the current ones.
$$ G \overset{\cdot}{=} r + \gamma r^{'} + \gamma^2 r^{''} + . . . + \gamma^{T-t} r_T $$
- The range of $\gamma$ is between 0 and 1. If it is 0, the agent will only consider the current reward. In most nontrivial examples, its is set between 0.9 and 0.99.
- One question that might arise is how to compute the *expected return*, given that it depends on future rewards. The answer is through iteration and learning which states and actions map to the best rewards.

### Predicting Rewards with the State-Value Function

- *Policy* is a mapping from states to actions. It is the probability of choosing an action given a state, $p(a\|s)$ , as is denoted by $\pi$.
- You can calculate the expected return from the return, $G$, expected from your current policy, $\pi$, given a state, $s$. This is called the *stage-value function* and is defined as

$$ V_{\pi}(s) = \mathbb{E}_{\pi}[G|s] = \mathbb{E}_{\pi}[\sum_{t=0}^{T} \gamma^t r_t | s] $$

- $V$ is the symbol to denote the value function
- The second part says that the value function is the expected value of the return, given a current state, following policy $\pi$.
- The third part is the expansion of terms as defined previously.

**Simulation using the state-value funfction**

- Consider a program that simulates positions of five squares. The agent starts and left most square and its goal is to real the right-most square. If it moves left, it fall off an imaginary cliff. The agent will get 5 points for reaching the right-most square and 0 points for falling off the cliff.
- For simplicity, policy is a random one, it either goes left or right with equal probability.
- Our goals it to calculate the expected reward for each state. We can do so by running the simulation for multiple epochs (agent reaching terminal state). The reward for each state is calculated as the total reward divided by the number of times the agent visited that state.


### Predicting Rewards with the Action-Value Function
- The previous methods shows you how to get rewards based on the current state but not all actions are the same. Some actions move you closer to the goal, some move you away.

$$ Q_{\pi}(s,a) = \mathbb{E}_{\pi}[G|s,a] = \mathbb{E}_{\pi}[\sum_{t=0}^{T} \gamma^t r_t | s, a] $$
- This is the *action-value function* for policy $\pi$.
- $Q$ is the same as the state-value function, but the rewards also depends on the action taken.

### Optimal Policies
- The policy that when followed generated the maximum expected return is called the *optimal policy* and is denoted by $\pi^*$. The equivalent state-value function is denoted by $V_*(s)$ and the action-value function is denoted by $Q_*(s)$.
- The state value function and the state-action value function both represent the same thing at a different level of granularity.
- Imagine you have good estimates for the expected values since you've run the simulation for a long time. You can then use these values to derive the optimal policy. Now, using the optimal policies, if you decide to get the values for $V$ and $Q$, you will get the same values. This is represented in mathematical terms as *Bellman optimality equation*.

$$ 
\begin{align*}
V_*(s) \overset{\cdot}{=} \arg\max_a Q_{\pi_{*}}(s, a) \\
\quad = \max_a \mathbb{E}_{\pi_{*}}[G \mid s, a]
\end{align*}
$$  

- This states that choosing the action that maximizes the action-value function at every step is the same as the optimal state value function.

## Monte Carlo Policy Generation
- In early example for the state value function, instead of randomly choosing actions, we could have chosen all permutation of actions and store the trajectory. But this would be impractical for large state spaces.
- Instead, we can use *Monte Carlo* algorithm.
- Imagine the environment is a black box, and we only know the distribution of the inputs. MC techniques randomly sample the input to observe what happens at the output. After adequate sampling, we can characterize the output distribution.
- In RL, MC methods can be defined as techniques that average the rewards returned over entire trajectories.


**On-policy MC Algorithm**

1. **Input:** a policy function, $\pi(a \mid s, Q_{\pi}(s, a))$

2. Initialize $Q(s, a) \leftarrow 0$, for all $s \in \mathcal{S}, a \in \mathcal{A}(s)$  
$Returns(s, a) \leftarrow [\,]$, for all $s \in \mathcal{S}, a \in \mathcal{A}(s)$  
3. **Loop:**

	4. Generate a full episode trajectory following $\pi$  
    5. Initialize $G \leftarrow 0$  
    6. **loop** for each step of episode, $t \doteq T-1, T-2, \dots, t_0$: 

        7. $G \leftarrow \gamma G + r$  
        8. **If** $(s, a)$ not in $(s_0, a_0), (s_1, a_1), \dots, (s_{t-1}, a_{t-1})$:  
            9. Append $G$ to $Returns(s, a)$  
            10. $Q(s, a) \leftarrow \text{average}(Returns(s, a))$  

- $Step (1)$ starts by passing a policy. Most MC algorithms have built-in policies, but you can also pass your own. The policy, $\pi(a|s,Q_\pi(s,a))$, returns an action for a given state using the action-value function. 
- $Step (2)$ initializes the action-value function and creates a buffer for all the interaction with the environment.
- $Step (4)$ generates a full episode trajectory by following the current policy. In this case, it uses a $\epsilon$-greedy policy.
- $Step (6)$ iterates over every step in the episode in reverse. This is important because the agent chooses actions based on *future* rewards. This step starts at the end and iterate backward summing rewards as it goes. Eventually, it will end up at the first state, and then we have one prediction of the expected return.
- $Step(7)$ sums up the rewards considering the discount factor.
- $Step (8)$ check if current state-action pair has been seen before. 
- If not, $step(9)$ adds it to the buffer.
- $Step (10)$ updates the action-value function by averaging all the returns for the current state-action pair.

## Value Iteration using Dynamic Programming
- Value iteration implements DP, but it depends on knowing the transition probabilities in full; you need a model of your environment.

![Value Iteration for generating an optimal policy](assets/img/value_iteration_DP.png)

- In $Step (1)$, you need to define the transition probabilities and a stopping threshold. In case of grid word, all the moves would have probability of 1 except for the ones that go out of bounds. The stopping threshold controls how long to keep refining the value estimate.
- $Step (2)$ initializes the state-value buffer for all states and a variable for maintaining the current amount of error.
- $Step (4)$ loop over all the states
- $Step (5)$ stores a local copy of the state-value estimate for the current state.
- $Step (6)$ finds the largest expected return after iterating over all possible next states and rewards (because even though you select action left, you might end up going straight).
- $Step (7)$ calculates the error: difference between the current state-value estimate and the new one.
- $Step (8)$ make sure that the loop only happens, if the error is larger than the threshold i.e. the change is meaningful.
- In $Step (9)$, the algorithm outputs a deterministic policy by choosing the action that maximizes the expected return for each state. 
