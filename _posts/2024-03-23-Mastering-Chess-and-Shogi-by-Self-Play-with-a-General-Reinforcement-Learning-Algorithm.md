---
layout: post
title: A Paper a Week - Week 12
subtitle: "Mastering Chess and Shogi by Self-Play with a General Reinforcement Learning Algorithm, Silver et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/alphaZero.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week12, paper]
---

### Introduction
For the twelfth post in this series, I read the chess and shogi extension of last week's paper on AlphaGo, "Mastering Chess and Shogi by Self-Play with a General Reinforcement Learning Algorithm" by Silver et al. This paper presents the AlphaZero algorithm, which is a generalization of the AlphaGo Zero algorithm to other games, and more specifically, any problem that could be "gamified" in a similar manner.

### Summary
The AlphaZero algorithm was designed to take the pre-existing AlphaGo Zero algorithm and generalize it so it can be applied to domains other than Go. To do so, the authors set their sights on two additional very popular and challenging board games, Chess and Shogi. Like Go, Chess and Shogi are both played between two players and have an unimaginable amount of possible board states, which makes them impossible to tackle from an reinforcement learning perspective without deep learning or extreme domain specific heuristic search, expert-level training, and hand-crafted features (e.g. DeepBlue, the chess agent IBM created that beat Gary Kasparov). However, unlike Go, Chess and Shogi don't have a symmetric board and have games that can also result in ties, rather than just wins or losses. Additionally, for a game like chess, there are many position-specific rules that make the game very asymmetric or hard to predict, like that a pawn can move two spaces on the second rank or that it can promote if it reaches the eigth rank. The same goes for the "en passant" move or "castling" which are non-trivial and involve more reasoning than just spatial. For these reasons, the convolutional architecture used for AlphaGo Zero isn't ideal for games like Chess or Shogi because the games are less reliant on spatial information and aren't translationally invariant (Go can be rotated 90, 180, 270 degrees and still be the same state). For these reasons, AlphaZero builds upon AlphaGo Zero in the following ways:
1. AlphaZero estimates and optimizes the probability of winning, rather than just predicting a W/L to account for the possibility of draws.
2. AlphaZero doesn't augment the input data (e.g. rotating the board state to quadruple the training data set) 
3. AlphaZero maintains a single deep neural network and updates its parameters continuously rather than waiting for an iteration of self-play to complete and picking the so-called "best player" to continue with.

With the simple above changes, AlphaZero was able to outperform SOTA computer agents on all three games - Chess, Shogi, and Go (even beating AlphaGo Zero 60-40 in 100 games). What I find so incredible about AlphaZero is that it defies traditional logic that making a more generalized solution will typically perform worse than a very domain-specific solution. Furthermore, it asserts the need for, and dominance of, deep learning within the field of Reinforcement Learning. For problems where the state space is too large for tabular methods or too complex for simple function approximation methods, deep learning still allow us to solve problems and do them at a super-human level.

### Questions/Notes I Have
1. Once again, I didn't go too deep into how AlphaZero works under the hood since I think the blog post would take a very long time and would contain a bit too much math/not much direction. If you're curious, I recommend reading the paper and specifically, the methods section, as it explains in greate detail how the algorithm works.
2. Here's a great summary [article](https://deepmind.google/discover/blog/alphazero-shedding-new-light-on-chess-shogi-and-go/) from DeepMind about AlphaZero. It has a lot of great external resources if you're curious to learn more about AlphaZero and how it works under the hood.