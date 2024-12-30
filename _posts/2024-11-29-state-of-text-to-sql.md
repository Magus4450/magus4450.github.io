---
title: "[WIP] State of Text-to-SQL"
date: 2024-11-29
categories: [Research Papers]
tags: [text-to-sql, llm, bird, spider]
math: true
---

# [WIP] State of Text-to-SQL

## Text-to-SQL

Databases are inaccessible to most people because they are not versed to query the database. This is where **Text-to-SQL** comes into play. Large Language Models (LLMs) can be used to convert natural language into query languages (SQL or GQL) which can then be used to query databases and get quantitative information. This is a very powerful tool for non-technical people. 

Consider a key-stake holder who wants to know the number of sales in the last quarter. They can simply ask the model and get the answer.


## Dataset

The two most popular datasets for Text-to-SQL tasks are **[SPIDER](https://yale-lily.github.io/spider)** and **[BIRD](https://bird-bench.github.io/)**, both of which are innovative in their own ways. SPIDER was the first dataset to introduce dataset having multiple databases and complex queries. BIRD, on the other hand, introduced *Valid Efficiency Score (VES)* which also focuses on the efficiency aspect of this task. BIRD is also a more comprehensive dataset than is well able to model the messy and noisy data in real-world.

## Metrics

There are three major metrics used across the two datasets. They are:

1. **Exact Matching (EM)**: Measures whether the predicted SQL query matches the reference query in structure, ignoring specific values.
2. **Execution Accuracy (EX)**: Compares if the execution result of the generated SQL matches the reference SQL output, allowing for multiple correct formulations.
3. **Valid Efficiency Score (VES)**: Evaluates the efficiency of the correct SQL queries in terms of execution time.



## Top Models

In this post, I will mostly be focusing on top models that were accompanied by a research paper. They are:

1. [DIN-SQL: Decomposed In-Context Learning of Text-to-SQL with Self-Correction](https://arxiv.org/abs/2304.11015) (*SPIDER 4th place*)
2. [Text-to-SQL Empowered by Large Language Models: A Benchmark Evaluation](https://arxiv.org/abs/2308.15363) (*SPIDER 2nd place*)
3. [The Death of Schema Linking? Text-to-SQL in the Age of Well-Reasoned Language Models](https://arxiv.org/abs/2408.07702) (*BIRD 5th place*)
4. [CHASE-SQL: Multi-Path Reasoning and Preference Optimized Candidate Selection in Text-to-SQL](https://arxiv.org/abs/2410.01943) (*BIRD 1st place*)


## DIN-SQL: Decomposed In-Context Learning of Text-to-SQL with Self-Correction

The authors of this paper wanted to explore the possible use of few-shot prompting in a field where fine-tuning models have been performing really well. They recalled LLM prompting techniques such as chain-of-thought, least-to-most and decomposition. In this paper, first they explored where the LLM fails in few-shot prompting and introduced a method that uses decomposition to divides the main task into multiple sub-tasks. Their proposed model beats the then SOTA model achieving an *execution accuracy* of 85.3% from 79.9% on the SPIDER dataset.

### Few-shot Error Analysis

The authors randomly sampled 5000 examples from the SPIDER dataset and classified the errors from the few-shot prompting into different categories. 37% of the errors were schema-linking where the model was unable to identify the column or table. 21% of the errors were JOIN related and 13% were GROUP-BY related.

### Methodology

Their approach to solve this issue was to divide the problem into smaller sub-problems and use those solutions to construct a solution for the original problem. While there may not be procedural structure in the SQL query, there is a step-by-step process in the thought process while writing the query. The authors divided this thought process into four steps:

1. Detecting database tables and columns that are relevant to the query.
2. Identifying the general query structure for more complex queries
3. Formulating any procedural subcomponents if they can be identified
4. Writing the final query based on the solutions of the sub-problems

Based on these four steps, the author proposed a model that contains four modules using only few-shot prompting:

1. **Schema Linking Module**: This module is responsible for identifying the tables and columns that are relevant to the query. It utilizes the prompt format "Lets think step by step". Each prompt starts with 10 task-specific examples following the chain-of-thought and ends with "Let's think step by step".
![DIN-SQL Schema Linking](assets/img/din-cot.png)

2. **Classification & Decomposition Module**: Sometimes a correct table or join condition is not detected, sometimes the queries might have procedural steps such an uncorrelated sub-queries, which may be generated independently and be merged into the main query. To solve these issues, this module first classifies each query into one of three classes: easy, non-nested complex and nested complex. It also detects the set of tables to be joined for both non-nested and nested queries.
![DIN-SQL Classification and Decomposition](assets/img/din-cad.png)

3. **SQL Generation Module**: As the query becomes more complex, more intermediate steps in required to bridge the gap between natural language and the SQL query. But adding intermediate steps for simpler queries degrades their performance. On the basis of complexity, the model decides whether to generate the SQL query in one step or multiple steps.

	a. For *easy class*, a simple few-shot prompting with no intermediate is adequate. The prompt follows the format $<Q_j, S_j, A_j>$ where $Q_j$ and $A_j$ give the query text in English and SQL respectively and $S_j$ indicates the schema link.

	b. The *non-nested complex class* includes queries that require join. The findings in the error analysis tells us that finding the right columns and foreign keys to join two tables are challenging for LLMs. To address this, the authors resort to an intermediate representation to bridge the gap between queries and SQL statements. Since operators like JOIN ON, FROM and GROUP BY have no clear counterparts in natural language, they are removed and operators like HAVING and WHERE are merged. This intermediate representation is called SemSQL. The authors decide to use NatSQL which builds upon SemSQL. The prompt follows the format $<Q_j, S_j, I_j, A_j>$ where $I_j$ represents the intermediate representation.

	c. The *nested complex class* contains the most sophisticated queries and require several intermediate steps. This class contains queries that not only require sub-queries using nesting and set operations such as EXCEPT, UNION, and INTERSECT but also multiple table joins. The prompt is designed in a way such that the LLM first solves the sub-queries, and uses them to generate the final answer. The prompt follows the format $<Q_j, S_j, <Q_{j1}, A_{j1}, . . . , Q_{jk}, A_{jk}>, I_j, A_j>$ where $k$ denotes the number of sub-questions, and $Q_{ji}$ and $A_{ji}$ respectively denote the $i$-th sub-question and the $i$-th sub-query.

4. **Self-correction Module**: This module is used to correct the minor mistakes made by the previous modules. The SQL generated from previous model is passed in a zero-shot setting to a LLM.

### Experiments and Results

The method was evaluated using two variants of CodeX family (Davinci and CUshman variants) and the GPT-4 model. The methods were evaluated against two datasets: SPIDER and BIRD using EX, EM and VES.

![DIN-SQL performance on SPIDER's test set](assets/img/din-spider.png)
The figure above shows the performance of DIN-SQL on the holdout test set for the SPIDER dataset. This method obtained the highest EX score of 85.3% (the top score at that time). The relatively lower EM score of 60% is due to the fact that other models learn the structure of SQL during fine-tuning.

![DIN-SQL performance on BIRD's test set](assets/img/din-bird.png)

The figure above shows the performance of DIN-SQL on the holdout test set for the BIRD dataset. The model achieved an EX score of 55.9% and a VES score of 59.44%. The model outperformed the previous SOTA model on EX by 1.01%.


### Limitations

- This method has a latency of 60 seconds and incurs a cost of $0.5 per query which is very high.

## DAIL-SQL: Text-to-SQL Empowered by Large Language Models: A Benchmark Evaluation



