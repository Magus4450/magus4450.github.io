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

