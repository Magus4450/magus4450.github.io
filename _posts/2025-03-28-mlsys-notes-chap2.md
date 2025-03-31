---
title: "Designing ML Systems Notes: Chapter 2"
date: 2025-03-23
categories: [System Design, ML Notes]
tags: [system design, machine learning]   
math: true
---


# Intro to ML and System Design

## Business and ML Objectives

- What is the objective of the proposed ML projects?
- Data scientists mostly only care about improving the performance of their ML models.
- Manager, on the other hand, focus only on the business metric. The main goal of a company is to maximize profit, hence we need to focus on the business goal.
- We can use a metric that maps business metric to ML metrics. Netflix uses a metric *take-rate* for their recommender system which is the number of quality plays divided by the number of recommendations a user sees.
- To gain a definite answer on the questions of how ML metrics influence business metrics, experiments like A/B testing needs to be conducted.

## Requirements for ML Systems

### Reliability

- system should continue to perform the correct function at the desired level of performance even in the face of adversity

### Scalability

- ML system can grow in complexity. You might be using a logistic regression model that fits in 1GB of RAM but next year you might be using a neural network that requires 16GB of RAM.
- ML system can grow in traffic volume.
- ML system can grow in ML model count.
- Autoscaling, although tricky to implement, is very important.
- Artifact management also equally important. When you have 1000s of models, it is very hard to monitors all of it. The process of monitoring and retraining needs to be automated.

### Maintainability

- There are ML engineers, DevOps engineers and subject matter experts working together in a same project.
- Important to structure workloads and set up your infrastructure such that different contributors can work using tool they are comfortable with.
- Code should be documented.
- Code, data, and artifacts should be versioned.
- Models should be reproducible.

 

### Adaptability

- To adapt to shifting data distributions and business requirements, the system should have some capacity for both discovering aspects for performance improvements and allowing updates without service interruption.

### Iterative Process

- Developing an ML system is an iterative process and in most cases, never-ending.

<img src="/assets/img/process-ml-dev.png" style="width:600px;" alt="Task Interference">

### Framing ML Problems

- As an ML Engineer, you should be able to break down business problems into ML problems.
- Say you're working at a bank and your boss tells you to develop an ML system to speed up the customer service. Speeding up customer service isn't necessarily an ML problem, but you can break it down into smaller problems which could be using ML to direct calls to the right department, using ML to transcribe calls, using ML to summarize calls, etc.

### Decoupling Objectives

- Say you're building a system that ranks item on a user's feed. For simplicity, we consider only two objectives, the quality and the engagement of the feed.
- The naive approach would be to create a single objective function that combines the two objectives as: $loss = \alpha \ \text{quality_loss} + \beta \ \text{engagement_loss}$
- You could change the value of $\alpha$ and $\beta$ to change the importance of the two objectives. The problem with this approach is that each time you tune $\alpha$ and $\beta$, you'll have to retrain the model.
- A better approach would be to train two model *quality_model* and *engagement_model* and combine the results of the two model for final prediction. You can now easily tune the values of $\alpha$ and $\beta$ without retraining the model.