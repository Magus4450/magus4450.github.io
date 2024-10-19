---
title: "RL Notes: Chapter 2.1"
date: 2024-10-18
categories: [Reinforcement Learning, ML Notes]
tags: [reinforcement learning, machine learning, armed bandit, epsilon-greedy]
math: true
---

# Markov Decision Processes, Dynamic Programming, and Monte Carlo Methods

- The foundation of RL is based on three topics
	- **Markov Decision Processes (MDPs)**
	- **Dynamic Programming**
	- **Monte Carlo methods**

## Multi-Arm Bandit Testing

- Say you're working as a software-dev and want to choose a color for a button - either red or green
- How do you quantify which one is better?
- For this, we need to define three core elements of the problem
	- the reward
	- the actions
	- and the environment

### Reward Engineering

- To quantify the performance, the result of action must be quantifiable
- Usually, good outcomes are encoded as positives values and bad outcomes as negative values (Although not necessary)
- For button example, we can say click is a positive outcome and sale is an even more positive outcome.
- Instead of assigning a different value for each click, we can set it to the average sales through each button.
- The reward must be simple enough so that the model can actually work towards the goal.
- Complex reward tends to overfit (Consider a maze, where the agent will generally be stuck in a wall near the goal if the reward is bad)

### Policy Evaluation: The Value Function

- In an environment, the agent can take multiple action $A$, from which it can do a particular action $a$. This is denoted as $a \in A$
- The environment then rewarded in the agent with the reward $r$ among $R$ total rewards which moved the environment into a new state, $s \in J$
- All these variables are stochastic meaning, if the agent takes the same action in the same state, the reward may not be the same
- The mathematical form for the button example would be $A\dot{=} \{a_{red}, a_{green}\}$
- Now, how do we quantify which action is better? One method is to calculate the average reward, $r_{avg}$, for a certain number of customers $N$, for each action, $a$, 
$$r^{avg} \dot{=} \frac{1}{N(a)} \sum^{N(a)}_{i=1} r(a)_{i} = \frac{r_{1} + r_{2} + . . . + r_{N(a)}}{N(a)}$$
- This works but you'd have you'd have to wait for a lot of customers before this reward can be computed
- Instead, we can use an **online algorithm** which updates as each interaction happens
In an online update in Reinforcement Learning (RL), the process is described by the following series of equations:

<p align="center">This is the same form as above</p>

$$
r_{\text{avg}} \leftarrow \frac{1}{N} \sum_{i=1}^{N} r_i
$$

<p align="center">We extract the last reward \( r_N \)</p>

$$
r_{\text{avg}} \leftarrow \frac{1}{N} (r_N + \sum_{i=1}^{N-1} r_i )
$$

<p align="center"> Add \( 1 = \left(N - 1\right) \frac{1}{N - 1} \)</p>


$$
r_{\text{avg}} \leftarrow \frac{1}{N} (r_N + \left(N - 1\right) \frac{1}{N - 1} \sum_{i=1}^{N-1} r_i )
$$

<p align="center"> The last part of the equation can be converted to average reward for \( N-1 \) actions</p>

$$
r_{\text{avg}} \leftarrow \frac{1}{N} r_N + \left(N - 1\right) r_{\text{avg}}^{(N-1)} 
$$

<p align="center"> Expand</p>

$$
r_{\text{avg}} \leftarrow \frac{1}{N} r_N + N r_{\text{avg}}^{(N-1)} - r_{\text{avg}}^{(N-1)} 
$$

$$
r_{\text{avg}} \leftarrow r_{\text{avg}}^{(N-1)} + \frac{1}{N} (r_N - r_{\text{avg}}^{(N-1)}) 
$$

- The final result equation looks like an exponentially weighted moving average, a common form of online algorithms to estimate the mean.
- It can be cleaned up to look like this:
$$ r \leftarrow r + \alpha (r - r')$$

### Policy Improvement: Choosing the Best Action

- Allowing the agent to choose its best action is what differentiates RL from ML
- Consider the same button example, say we have already decided what the reward will be for each button
- We can show one button 50% of the time and the other 50% of the time, later we can sum up the reward to choose the best one
- But this is not optimal because 50% of the users will be shown a suboptimal button
- Another option could be to start showing each button 50% of the time but slowly switch to a more optimal button over time. This is the idea behind **bandit algorithms**
- This strategy highlights an important concept in RL, **exploration vs exploitation**
	- **Exploration**: Trying out new things to see if they are better
	- **Exploitation**: Using the best known strategy to maximize reward

#### $\epsilon$-greedy algorithm
- One generalized algorithm to balance exploration and exploitation is the $\epsilon$-greedy algorithm
![$\epsilon$-greedy algorithm](assets/img/ep-greedy.png)

- Step (1) tell us to take an input value for $\epsilon$ (usually a small value) that is between 0 and 1
- Step (2) initializes arrays to store the number of times each action has been taken and the average reward for each action
- Step (3) is to loop forever
- In Step (4), for $1 - \epsilon$ of the time, the algorithm chooses certain action that maximizes the reward (exploitation), and for $\epsilon$ of the time, it chooses a random action (exploration)
- Step (5) does that action in the environment to get a reward
- Step (6) updates the counter for the chosen action
- Step (7) updates the average reward for the chosen action

### Simulating the Environment

- Often the best environment is the real world, but it can be expensive, dangerous or complicated to developed against
- Instead, we can create our own simulation of the environment based on the data
- For the button example, let's consider 40% of the users click the red button and only 5% of them the green button
- We can create a simple function when given a button, it returns a reward based on the probability of the button being clicked (In our case, reward is 1 for simplicity)
![Website Simulator Algorithm](assets/img/website-sim.png)

### Running the Experiment

- The flow is as follows:
	- The user open the website
	- The agent chooses a button to show
	- The user click the button and that action is sent to the agent
	- The agent updates the reward and the average reward for the action

![$\epsilon$-greedy simulation](assets/img/e-greedy-sim.png)

- The plot above shows the results for different values of $\epsilon$ and the number of steps it takes to reach its optimal action (asymptotically)
- For $\epsilon = 1.0$, the agent takes random action every time, so the probability of optimal action is 50%.
- For $\epsilon = 0.5$, the agents start off with less probability of choosing the optimal action but eventually converges at around 75%.
- For $\epsilon = 0.1$, the agent starts off with an even lower probability of choosing the optimal action but converges at around 90%.
- We can also see that when the value of $\epsilon$ is low, the agent takes longer to converge to the optimal action, so choosing the right value is crucial.

### Improving the $\epsilon$-greedy Algorithm

- Algorithms are fundamentally limited by how efficiently they can explore and how quickly they can explore
- Here, the hyperparameter $\epsilon$ controls the trade-off, too high the value, the agents keep on exploring, too less and the agent doesn't explore enough
- Also, if the difference in reward between two actions are very less, the agent will have a hard time choosing the optimal action (because of *law of large numbers*)
- It is often better to choose the action upon the current estimates of the distribution of rewards. (Instead of returning a single action, the agent returns the probability of each action, weighted by expected rewards of each state aka **Softmax**)
- We can also slowly decay the value of $\epsilon$ over time to allow the agent to explore more in the beginning and exploit more later on. This is called *annealing*
- Another popular improvement is to add a *bonus* to actions that haven't been explored more. This is called *Upper Confidence Bound (UCB)* and doesn't have a fixed value of $\epsilon$.

![Exploration Comparison](assets/img/exploration-comparison.png)
