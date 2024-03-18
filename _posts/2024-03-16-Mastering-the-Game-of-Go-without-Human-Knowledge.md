---
layout: post
title: A Paper a Week - Week 11
subtitle: "Mastring the Game of Go without Human Knowledge, Silver et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/alphaGo.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week11, paper]
---

### Introduction
For the eleventh post in this series, I decided to read one of my favorite papers, "Mastering the Game of Go without Human Knowledge", by Silver et al. This paper presents the AlphaGo Zero algorithm, which was an improvement to the algorithm used to beat the 18-time world Go champion, Lee Sedol. AlphaGo marked the first time an AI has achieved superhuman performace in the game of Go, a feat previously thought of as unachievable due to the size of the state space ($10^{170}$ states). On top of that, AlphaGo Zero beat the AlphaGo player 100-0.

### Summary
The primary idea presented in this paper was to explain the differences between the AlphaGo player and the novel AlphaGo Zero player. There were three significant differences between the two approaches:
1. The former consisted of two deep neural networks, one called the "policy network" which was used to output move probabilities, and the "value network" which outputs a position evaluation. The latter merged these two networks into a single network that takes the current board state, $s$, as input and outputs a vector of move probabilities, $p$, and a value $v$ which is a scalar estimation of the probability of the player winning from the current position, $s$.
2. AlphaGo Zero is trained purely on self-play. That is, there's no pre-training on human moves or anything to bootstrap behavior. It simply has the rules of the game and the board state as input and then is let loose to play against itself for thousands and thousands of episodes. This approach enables AlphaGo Zero to not only re-discover high-level human behavior, but to also discover previously unknown strategies due to not being biased by any human moves to begin with.
3. AlphaGo Zero uses a deep residual network with only the board as input while AlphaGo uses a shallower convolutional network for both the policy and value network and also requires way more computing power than AlphaGo Zero (by a factor of 12).

That said, there were also plenty of similarities between the approaches. First, both approaches use what's known as Monte-carlo Tree Search, or MCTS, to perform lookahead search, which acted as a "policy improvement operator". In order to aid the MCTS, the policy network, in the AlphaGo case, and just the single network, in the AlphaGo Zero case, is used to inform which branches to explore. Additionally, both approaches use the same loss function during training:
$$l=(z-v)^2-\pi^T\text{log}p+c||\theta||^2$$
This loss function combines the MSE loss for the value network (error between the true winner from the last iteration(s) of self play) and the cross-entropy loss of the policy network (similarity between the network's move probabilities and the probabilities returned from MCTS).

While I breezed over a lot of the actual methods used (i.e. how does MCTS work, how many layers does each network have, etc.), what I find to be so remarkable about AlphaGo Zero is that you can take an approach that was trained for months (AlphaGo) and simply by optimizing and improving the already superhuman approach, create an approach that can beat the previous one with only 36 hours of training. AlphaGo Zero represents an enormous leap in the state-of-the-art approaches to board game agents. This in turn, can be applied to many other real-world problems by framing them as an RL objective that AlphaGo Zero can be applied to.

### Questions/Notes I Have
1. Here's a great summary [article](https://deepmind.google/technologies/alphago/) from DeepMind about AlphaGo. They have a short film (90 minutes) at the bottom of the article that I highly highly recommend and is one of my initial inspirations to get into the field of AI.