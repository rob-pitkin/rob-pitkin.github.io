---
layout: post
title: A Paper a Week - Week 16
subtitle: "Monte-Carlo Tree Search and Rapid Action Value Estimation in Computer Go - Part 2"
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/rave.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week16, paper]
---

### Introduction
For the sixteenth post in the series, I continued reading "Monte-Carlo Tree Search and Rapid Action Value Estimation in Computer Go" by Sylvain Gelly. This week I'll be covering the rest of the paper, which discusses the application of MCTS to the game of Go and the introduction of Rapid Action Value Estimation (RAVE) to the algorithm. I took a 3-week break from this series due to a busy schedule as well as vacationing, but I'm excited to start back up.

### Summary
The RAVE algorithm is a heuristic extension to the MCTS algorithm. The heuristic, called "All Moves As First" (AMAF), is based on the idea of having a general state-action value for every move, regardless of when it's played. The AMAF value function is defined as the expected outcome, $$z$$ from state $$s$$, when following a joint policy $$\pi$$ for both players, given that action $$a$$ was selected at some subsequent turn. In other words, the AMAF value is the expected outcome of the game when action $$a$$ is played at some point in the future. The AMAF value function can be approximated via Monte-Carlo simulation with the mean outcome of all simulations:

$$Q^{AMAF}(s,a) = \frac{1}{N^{AMAF}(s,a)}\sum_{i=1}^{N(s)} I^{AMAF}_i(s,a)z_i$$

where $$I^{AMAF}_i(s,a)z_i$$ is an indicator function that is 1 if state $$s$$ was encountered at any timestep $$t$$ of the $$i$$th simulation and action $$a$$ was taken at any timestep $$u >= t$$, and 0 otherwise. The RAVE algorithm then combines this AMAF heuristic with MCTS by replacing the MC value function with the AMAF value function. The core assumption of RAVE is that within a subtree of a current state, the value of an action in the current state will be similar to the value of the action anywhere in the subtree. However, in practice, the RAVE algorithm learns very quickly, but is inherently inaccurate as its underlying assumption is not always true.

To address the inaccuracy of the RAVE algorithm, the authors introduce MC-RAVE, which combines the rapid learning ability of RAVE with the accuracy and convergence properties of MCTS. The MC-RAVE algorithm uses a weighted average of the MC value function and the AMAF value function to estimate the value of a state-action pair:

$$Q^{MC-RAVE}(s,a) = \beta Q(s,a) + (1 - \beta)Q^{AMAF}(s,a)$$

where $$\beta$$ is a scheduled weighing factor that determines the weight of the MC value function and increases based on the visit statistics of the current state-action pair. That is, when a state-action pair has only been visited a few times, the RAVE estimation has a larger influence on the value and as the number of visits grows, more weight is given to the MC value estimation. The MC-RAVE algorithm is then used in the MCTS algorithm in place of the MC value function. The authors show that MC-RAVE is able to outperform many more traditional search methods in a variety Go-playing scenarios, such as 9x9, 13x13, and 19x19 board sizes. There are a couple more extensions to MC-RAVE that are discussed in the paper, such as UCT-RAVE and using heuristic prior knowledge to improve the performance of the algorithm, which led to the final agent, called MoGo, achieving an ELO of 2500 (compared to the 1800 ELO of more traditional search agents). However, I won't be covering those since they require quite a bit of derivation and explanation and don't add much to the core concepts of the paper.

### Questions/Notes I Have
1. Here's a link to the [paper](https://www.cs.utexas.edu/~pstone/Courses/394Rspring11/resources/mcrave.pdf) for anyone curious about learning more.