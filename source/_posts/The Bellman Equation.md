---
title: 贝尔曼方程(The Bellman Equation)
date: 2018-09-10 
categories: 
		- 强化学习
tags:  
		- 强化学习
		- 原理
		- Reinforcement Learning
---

# 基本原理
贝尔曼方程（Bellman Equation）也被称作动态规划方程（Dynamic Programming Equation）

$$
Q^{\pi}(s, a)=\sum_{s^{\prime}} \mathcal{P}_{s s^{\prime}}^{a}\left[\mathcal{R}_{s s^{\prime}}^{a}+\gamma \sum_{a^{\prime}} \pi\left(s^{\prime}, a^{\prime}\right) Q^{\pi}\left(s^{\prime}, a^{\prime}\right)\right]
$$

其中，转移概率 $\mathcal{P}$ 为

$$
\mathcal{P}_{s s^{\prime}}^{a}=\operatorname{Pr}\left(s_{t+1}=s^{\prime} | s_{t}=s, a_{t}=a\right)
$$


期望奖励为 $\mathcal{R}$ 

$$
\mathcal{R}_{s s^{\prime}}^{a}=\mathbb{E}\left[r_{t+1} | s_{t}=s, s_{t+1}=s^{\prime}, a_{t}=a\right]
$$

从状态 $s$ 开始，经过动作 $a$ ，以概率 $\mathcal{P}_{s s^{\prime}}^{a}$ 转到状态 $s^{\prime}$ ， 期望奖励为 $\mathcal{R}_{s s^{\prime}}^{a}$ 。




# 参考
-   [Reinforcement Learning: an introduction” by Sutton and Barto (freely downloadable)](http://incompleteideas.net/book/the-book-2nd.html)
-   [Understanding RL: The Bellman Equations](https://joshgreaves.com/reinforcement-learning/understanding-rl-the-bellman-equations/)