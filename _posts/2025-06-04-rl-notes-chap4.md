---
title: "RL Notes: Chapter 4"
date: 2025-06-04
categories: [Reinforcement Learning, ML Notes]
tags: [reinforcement learning, machine learning, markov-decision-processes]
math: true
---

# Deep Q-Networks

- Tabular reinforcement learning (RL) algorithms, such as Q-learning and SARSA, use a table to store the action-value function. The size of the table grows exponentially with the number of states and actions, making it impractical for large state spaces.
- Also, if the action in continuous, the table becomes infinite. But, continuous action is desirable in many cases.
- The most common solution is to use a function that approximates the action-value function. This is a regression problem.
- The function should be chosen according to the nature of the problem. If the expected values grow exponentially near the goal, a linear function would not be able to approximate the action-value function well.
- Most commonly used model is a *deep artificial neural network*.

## Deep Reinforcement Learning
- Using DL allows to agent to accept any form of information, and delegate the modelling to the Neural Network. If we have states based upon images, we can use CNN, if it is based on time series, we can use RNNs, and so on.
- Using DL in RL is more difficult in ML. The agent has to wait a long time to receive any feedback. DL is already time-consuming, and RL adds to that.

## Deep Q-Learning
- Before 2013, researchers focused on using linear model to approximate the action-value functions, since according to them, it retained the same convergence guarantee as tabular methods.
- Eventually, they designed a DL based RL algorithm called *Deep Q-Networks (DQN)*.
- The realized the main problem was the moving goalpost. Imagine trying to swat a fly. The fly's movement is so random that you have to wait until the fly is stationary to swat it. Until the data is stationary, the model will not be able to learn anything.
- Model optimizers like Stochastic Gradient Descent (SGD) assume that your data is Independent and Identically Distributed (IID) random variables. Just from the definition of MDP, the current state depends on the previous state, which violates the IID assumption.


### Experience Replay
- The initial solution was to use *experience replay*, which is a technique that stores the agent's experiences in a replay buffer and samples from it to train the model. By taking a random subset when training, you break the correlation between consecutive observations.
- Size of the experience replay buffer is also really important. Agents can "catastrophically forget" experiences when observations drop off the end of a *first in, first out* (FIFO) buffer.

### Q-Network Clones
- In the original DQN algorithm, the same researchers proposed closing the Q-network. This leads to two NNs: one *online network* that produces actions and one *target network* that continues to learn. After some iterations, the prediction network copies the current copies the current wights of the target network.
- This made the algorithm more stable and less likely to oscillate or diverge. 
- The trick is to set this period small enough so that you can improve the model quickly, but long enough to keep the data stationary.

### Rainbow DQN
- In 2017, researcher created Rainbow DQN, using six extensions that each addressed fundamental concerns with Q-learning and DQN.
- The six extensions are:

#### 1. Double Q-learning
- Addresses overestimation bias by using the online network to select actions and the target network to evaluate them.

#### 2. n-step returns
- Accumulates rewards over multiple steps, which helps to reduce variance and improve learning efficiency.

#### 3. Distributional RL
-  The most fundamental deviation from standard DQN is the reintroduction of the probability into the agents.
- All Q-learning agent derive form the Bellman equations, which defines the optimal trajectory as the action that maximizes the *expected* return. Whenever we use a summary statistic, you are implying assumptions about the underlying distribution. If we choose to use mean, we are assuming that the data is distributed normally.
- Imagine a grid-world where the agent can move in four directions. If the agent falls off a cliff, it receives reward of -1, and if it reaches the goal, it receives a reward of +1. The expected returns for some states, will be close to 0. In these states, the average expected value yields actions that make no difference. This can cause the agent to wander aimlessly. In practice, this leads to convergence issues.
- Learning the *distribution of rewards* can lead to more stable learning.

$$ Z(s, a) \sim R(s, a) + \gamma Z (S', A') $$
- Here, $R$ is a stochastic reward that depends on state and action, and $Z$ represents the distribution of rewards.
- The expectation of $Z$ is the Q-value.
- The policy $Z^{\pi}$ is a mapping of state-action pairs to a distribution of expected rewards.

#### 4. Prioritized Experience Replay
- In DQN, transition are sampled uniformly from the replay buffer. The batch is likely to contains transitions that the agent has seen multiple times.
- It is better to include transitions from areas in which there is much to learn.
- *Prioritized Experience Replay* samples the replay buffer with a probability proportional to the absolute error of the temporal-difference update.

#### 5. Noisy Nets
- In complex environments, it may be difficult for the $\epsilon$-greedy algorithm to stumble across a successful results.
- *Noisy nets* augments $\epsilon$-greedy search by adding noise in the NN. The model will learn to ignore the noise when it experiences enough transitions for a given state.

#### 6. Dueling Networks
- In Q-learning algorithms, the Q-value estimates are often similar. In the classic CartPole envrionment, the first action of moving right or left doesn't matter much.
- It makes sense to learn the state-value function as opposed to the action-value function.
- *Advantage function* measures how much better an action is compared to the average action in a given state. It is given by: 
$$A^{\pi}(s, a) \doteq Q^{\pi}(s, a) - V^{\pi}(s)$$
- *Dueling networks* achieve this by creating a single NN with two *heads*; one is responsible for estimating the state-value function and the other for estimating the advantage function. The NN combines these two heads to generate the required action-value function.
- The benefit is that the sate-value function is quicker and easier to train, which leads to faster convergence.