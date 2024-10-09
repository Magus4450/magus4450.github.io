---
title: "RL Notes: Chapter 1"
date: 2024-10-09
categories: [Reinforcement Learning, ML Notes]
tags: [reinforcement learning, machine learning]     # TAG names should always be lowercase
---



#### A Analogy
- Philosopher have always pondered where knowledge comes from: from rationality (within us) or from empiricism (experience)
- Studying does help gain knowledge but experience is the main factor
- Best example of this is a child learning about new things
	- Consider a child climbing a cupboard to get a cookie (They learn than in order to get the cookie, they need to climb)
	- Or maybe a child touching a fire (from touching the fire, they now know that the fire is hot and shouldn't be touched)


#### Why Now?
- We have large data and large machines to process them all


#### Reinforcement Learning (RL)
- RL tells a system how to make the best decision, sequentially, to maximize a real-life measure of success. It learns this through trial-and-error.
- Everything a system does/can do in an *action*, eg: when riding a bike, to pedal or not?, how fast to pedal, when and how much to steer and so on.
- The *environment* is anything the system can interact with. Could be simulation or real life.
- Here, system is *agent*, the entity that can make decisions.
- *Reward* encodes the challenge which tells the *agent* if the *action* they performed was any good
- The main problem in RL is to define the *reward*. Consider a herbivore animal. Their goal is to survive. For that they need to eat. Just because they need to survive doesn't necessarily mean that can eat about anything they find.


#### When to use RL
- People apply RL where ML is commonly used but this is a wrong practice. Consider stock market prediction, there is a lack of clear agent or action.
- RL is best used when an action taken by an agent explores the environment (think robotics)
- Business metrics can easily be optimized using RL (Consider a company trying to maximize ad revenue)


#### RL Applications
- Agents in games (DeepMind's AlphaGo Zero for Go, OpenAI Fice for Dota 2)
- Robotics
- Cloud computing (Optimize applications for latency, power efficiency/usage. Datacenter cooling, CPU cooling, network routing)
- Finance (Portfolio allocation)
- Traffic Light Controls and so much more


#### Taxonomy of RL Approaches

##### Model-Free or Model-Based
- *Model-Based* algorithms use definitive knowledge of the environment they are in to improve learning
- For example, board games limit the moves that you can make. This knowledge can be used to
	- constrain the algorithm to limit invalid actions
	- improve performance by projecting forward in time (if I do this, and if the opponent does that, I should do)
- They learn efficiently because they don't waste time searching random paths**
- *Model-Free* algorithms can be used for almost anything
- They learn strategies by interacting with the environment.
- They figure out the rules through interaction


##### How Agents Use and Update Their Strategy
- The goal of any RL agent is to learn a strategy (specifically a *policy*)
- How and when the algorithm updates the strategy is very crucial for its performance
- *Online-agents* updates their strategy using only the data they've just observed
- But experts agree that SOTA algorithms can benefit from using past data
- *Offline-agents* can learn from offline datasets or logs. (Can be helpful when we don't have access to real-time data)
- In the real word, a mix of both is used
- Another difference depends on how agents select the action defined by its strategy.
- *On-policy* agents learn to predict the rewards of being in particular states after choosing actions according to the current strategy. 
- *Off-policy* agents learn to predict the reward after choosing any action.


##### Discrete or Continuous Actions
- Sometimes the agents needs to make decision like pressing the accelerator, or switching on a light.
- These two actions are different in a sense that, when you press the accelerator, you need to apply a certain amount of force (That force could be a few Newtons to larger values) The actions here is continuous
- The act of switching a light, on the other hand, is a binary action. You either turn it on/off or leave it be.


##### Optimization
- Like ML-based algorithms, RL algorithms need to find the optimal solution through some method
- In *value-based* algorithms, you try as many actions and records the results. Later you use this knowledge (the action that provided the best result) to guide the agent.
- In *policy-based* algorithms, a model in maintained and the parameters are tweaked.
- Less popular, *imitation-based* algorithms optimize by trying to mimic an expert.


##### Policy Evaluation and Improvement

![An interpretation of how algorithms update their strategy.](assets/img/1-2-how-algos-updates.png)
An interpretation of how algorithms update their strategy.
- The way in which an algorithms improves it strategy can be understood through *policy evaluation* and *policy improvement*
- First, the agent follows the strategy and generates data describing the state of the environment.
- From this new data, the agent attempts to predict the reward from current state of the environment; it evaluates the current policy.
- It uses this prediction to decide how to improve the policy; it could be choosing a better rewarding action or exploring. (Either is good)


#### Fundamental Concepts in Reinforcement Learning
- The first concepts of RL was originated in the work on psychology of animal learning.
- Russian physiologist *Ivan Pavlov* noticed that dogs salivate when introduced to food.
- When showing the dog some food, he also made a sound with a whistle.
- The dog could then product saliva on just the sound of the whistle.


##### The First RL Algorithm
- Similar to *Pavlov*, *Roberta Rescorla* and *Allan Wagner* did some experiments with a rabbit.
- They first blew a puff of air into the rabbit's air causing it to blink.
- They trained the rabbit to associate the puffing with an external sound. The rabbit then started to blink when hearing the external sound.
- Then they trained the rabbit to blink when exposed to both sound and light.
- When the rabbit hear a sound and saw the light, it blinked without puff.
- Next, when the researcher only flashed the light, the rabbit didn't blink.

What happened here is that the rabbit developed a hierarchy of expectations. It equated sound and light with blinking but when it didn't hear the sound, it didn't blink because it didn't meet its base expectation (equating sound with blink). This describes a method called *value estimation*.



*Get the book from [OREILLY](https://www.oreilly.com/library/view/reinforcement-learning/9781492072386/)*

