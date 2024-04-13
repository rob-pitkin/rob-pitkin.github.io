---
layout: post
title: A Paper a Week - Week 15
subtitle: "Monte-Carlo Tree Search and Rapid Action Value Estimation in Computer Go"
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/UCT.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week15, paper]
---

### Introduction
For the fifteenth post in the series, I read "Monte-Carlo Tree Search and Rapid Action Value Estimation in Computer Go" by Sylvain Gelly. The paper presents a survey of the Monte-Carlo Tree Search (MCTS) algorithm and its application to the game of Go (pre-AlphaGo). However, because the paper is quite long, I'll only be covering the MCTS-focused portion and the introduction of UCT (Upper Confidence Bound for Trees) in this post.


### Summary
I covered MCTS a bit last week, but I will revist the basics of the algorithm here. MCTS is a tree search algorithm that is used to approximate the value of a state in a game. The algorithm is based off of two phases, the expansion phase and the evaluation/backup phase. The expansion phase starts with a single-node tree, with the root being the current game state, and iteratively performs simulated sample episodes from the current leaf node (the root for t=0) to a terminal state. As an episode is being simulated, a new node is being stored in the tree for each simulated state and at each node the number of times the state has been visited as well as the sum of its experienced values and squared sum of experienced values is stored. Once a simulation reaches the terminal state of an episode, the algorithm switches over to the evaluation/backup phase where each of the nodes experienced during the episode are updated with the value of that state (the sum of discounted rewards in the simplest case) as well as the number of times that state was visited:

$$Q(s,a) = \frac{1}{N(s)} * \sum_{i=1}^{N(s)}I_i(s,a)z_i$$

where $$Q(s,a)$$ is the value of state $$s$$ and action $$a$$, $$N(s)$$ is the number of times state $$s$$ has been visited, $$I_i(s,a)$$ is the indicator function that is 1 if the $$i$$th visit to state $$s$$ was due to action $$a$$, and $$z_i$$ is the reward received at the $$i$$th visit to state $$s$$.

After a single iteration is completed and several episodes have been simulated from the current leaf, the best immediate child node, defined by the max reward, is chosen to be the next action taken and that node becomes the new leaf to start from for the next iteration. It's also worth noting that the backup update can be done incrementally instead of having to simulate entire episodes before updating the values of the nodes:

$$N(s_t) = N(s_t) + 1$$

$$N(s_t,a_t) = N(s_t,a_t) + 1$$

$$Q(s_t,a_t) = Q(s_t,a_t) + \frac{z - Q(s_t,a_t)}{N(s_t)}$$

One of the main ideas discussed in this survey is the introduction of UCT, or Upper Confidence Bound for Trees, which is a strategy for accurately balancing exploitation and exploration while assigning state values during MCTS. The idea is to use a selection strategy that is based on the value of the node as well as the number of times the node has been visited. The selection strategy is defined as:

$$Q^{UCT}(s,a) = Q(s,a) + c\sqrt{\frac{\log(N(s))}{N(s,a)}}$$

$$a^* = \arg\max_a Q^{UCT}(s,a)$$

where $$Q(s,a)$$ is the value of state $$s$$ and action $$a$$, $$N(s)$$ is the number of times state $$s$$ has been visited, $$N(s,a)$$ is the number of times action $$a$$ has been taken from state $$s$$, and $$c$$ is a constant that is used to balance exploitation and exploration. The idea is that the first term in the equation is the exploitation term, which is the average value of the state-action pair, and the second term is the exploration term, which is the square root of the log of the number of times the state has been visited divided by the number of times the state-action pair has been visited. The exploration term will decrease as the number of times the state-action pair has been visited increases, which will allow the algorithm to focus more on the exploitation term as the number of visits increases.

Next week I'll cover the rest of the paper, which discusses the application of MCTS to the game of Go and the introduction of Rapid Action Value Estimation (RAVE) to the algorithm.

### Questions/Notes I Have
1. Here's a link to the [paper](https://www.cs.utexas.edu/~pstone/Courses/394Rspring11/resources/mcrave.pdf) for anyone curious about learning more.