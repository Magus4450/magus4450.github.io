---
title: "RL Notes: Chapter 3"
date: 2025-05-30
categories: [Reinforcement Learning, ML Notes]
tags: [reinforcement learning, machine learning, markov-decision-processes]
math: true
---

# Temporal-Difference Learning and Q-Learning

- Monte Carlo methods try to estimate the value functions by running the agent for multiple episodes and averaging the returns. They can do so without explicit knowledge of the environment and the transition probabilities.
- Conversely, dynamic programming methods *bootstrap* by updating the policy after a single time step. But they must have complete knowledge of the environment.
- *Temporal-difference* (TD) learning is a combination of the two. It learns directly from experience by sampling, but also bootstraps.

## Formulation of Temporal-Difference Learning

$$ \begin{align*}
V_\pi(s) &\doteq \mathbb{E}_\pi[G \mid s] \\
         &\leftarrow \mathbb{E}_\pi \left[ V_\pi(s) + \alpha \left( G - V_\pi(s) \right) \mid s \right]
\end{align*}$$  
- This is the online realization of the MC esimate of the state-value function. The key part here is, $G$, which is the total expected return. So, the problem here is that you need to wait until the end of the episode to get the total return. 

$$ \begin{align*}
V_\pi(s) &\doteq \mathbb{E}_\pi[G \mid s] \\
         &\leftarrow \max_a \sum_{s', r} p(s', r \mid s, a)[ r + \gamma V_\pi(s') ]
\end{align*}$$  
- This is the equation for online dynamic programming state-value function. Important thing to note here is that this used a different interpretation of the state-value function. We can use this definition in the online MC equation.

$$V_\pi(s) \doteq \mathbb{E}_\pi[V_\pi(s) + \alpha (r + \gamma V_\pi(s') - V_\pi(s)) \mid s]$$
- We can convert this to an online algorithm, which assumes that an exponentially weighted average is a suitable estimate for the expectation opeartor.

$$ V_\pi(s) \leftarrow V_\pi(s) + \alpha (r + \gamma V_\pi(s') - V_\pi(s))$$

### Q-Learning
- We can reformulate the above equation to use the action-value function.

$$ Q(s, a) \leftarrow Q(s, a) + \alpha (r + \gamma \argmax_{a_s \in A(s)} Q(s', a_s) - Q(s, a)) $$
- We can think of everything right of $\alpha$ as *delta*, a measure of how wrong the current estimate is. 
- This equation allow the agent to look ahead to see which future trajectories are optimal, and this is called *rollout*.
- The active direction of the agent is called Q-Learning.

![Q-Learning](assets/img/q_learning.png)

- $Step (1)$ being by passing a policy (for example $\epsilon$-greedy)
- $Step (2)$ initializes an array to store all the action-value estimates.
- $Step (4)$ resets the agent to the initial state.
- $Step (6)$ chooses an action based on the policy. Ties are broken randomly.
- $Step (7)$ takes the action and observes the next state and reward.
- $Step (8)$ updates the action-value estimate using the Q-Learning update rule.
- $Step (9)$ updates the current state to the next state.
- $Step (10)$ checks if the episode is done. If it is, it goes back to $Step (4)$, otherwise it goes back to $Step (6)$.  


### SARSA
- SARSA was developed shortly after Q-Learning, and is a more general solution to TD learning.
- The main difference is the lack of argmax in the delta, insead it calculates the expected return by averagin over all runs (in an online manner).

$$ Q(s, a) \leftarrow Q(s, a) + \alpha (r + \gamma Q(s', a') - Q(s, a)) $$

![SARSA](assets/img/sarsa.png)

- One difference from Q-learning is that the algorithm chooses the next action, $a'$, before it updates the action-value estimate. $a'$ updates the policy and directs the agent on the next step, which makes this an *on-policy* algorithm.

