---
layout: post
title: A Paper a Week - Week 17
subtitle: "Deep Reinforcement Learning with Double Q-learning"
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/doubleQLearning.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week17, paper]
---

### Introduction
For the seventeenth post in this series, I read "Deep Reinforcement Learning with Double Q-learning" by van Hasselt et al. This paper introduces a new algorithm called Double DQN, which extends the DQN algorithm by integrating Double Q-learning in order to address the overestimation bias of Q-learning. The authors show that the Double DQN algorithm is able to outperform the original DQN algorithm on a variety of Atari games and even achieve super-human performance on more games than the original DQN algorithm.

### Summary
The Double Q-learning algorithm is an extension of the Q-learning algorithm that addresses the overestimation bias of Q-learning. The overestimation bias arises from the fact that the Q-learning algorithm uses the same values to select and evaluate an action, which can lead to overestimation of the value of the action. The Double Q-learning algorithm addresses this issue by using two separate Q-value functions, Q and Q', to select and evaluate actions, respectively. The Q' function is used to evaluate the value of the action, while the Q function is used to select the action. This updated Q-value target (using function approximation instead of tabular methods) is defined as:

$$Y_{t}^{DoubleQ} = R_{t+1} + \gamma Q(S_{t+1},argmax_{a}Q(S_{t+1},a;\theta_{t});\theta_{t}')$$

where $$\theta_{t}$$ and $$\theta_{t}'$$ are the parameters of the Q and Q' functions, respectively, and $$Y_{t}^{DoubleQ}$$ represents the target, also known as the TD-error. One can compare this new target to the unraveled original Q-learning target:

1. $$Y_{t}^{Q} = R_{t+1} + \gamma max_{a}Q(S_{t+1},a;\theta_{t})$$

2. $$Y_{t}^{Q} = R_{t+1} + \gamma Q(S_{t+1},argmax_{a}Q(S_{t+1},a;\theta_{t});\theta_{t})$$

where the only real difference is the use of the Q' function in the Double Q-learning target (via the $$\theta_{t}'$$ parameter).

Since DQN naturally uses two networks during training - the first known as the target network, which is used to calculate the target Q-value and is only updated every $$T$$ timesteps, and the second known as the online network, which is used to select the action - the authors recognized that DQN easily lends itself to the Double Q-learning algorithm without having to perform any significant modifications. By replacing $$\theta_{t}'$$ with the target network, $$\theta_{t}^{-}$$, the authors trivially implement the Double DQN algorithm:

$$Y_{t}^{DoubleDQN} = R_{t+1} + \gamma Q(S_{t+1},argmax_{a}Q(S_{t+1},a;\theta_{t});\theta_{t}^{-})$$

With the simple change of decoupling the action selection from the evaluation (by removing the max operation and replacing it with a nested argmax operation), the authors significantly reduce the overestimation bias of the DQN algorithm.

In order to perform a fair and controlled comparison of the Double DQN algorithm to the original DQN algorithm, all evaluation experiments were run with the exact same games and with the same hyperparameters as the DQN paper (Mnih et al., 2015). In fact, the only difference between the two training and evaluation procedures is the use of the Double Q-learning target instead of the original Q-learning target. This initial comparison already showed that the Double DQN algorithm was able to outperform the original DQN algorithm on a variety of Atari games, but the authors further increased performance by tuning the hyperparameters of the Double DQN algorithm to achieve additional state-of-the-art results.


### Questions/Notes I Have
1. Here's a link to the [paper](https://arxiv.org/abs/1509.06461) for anyone curious about learning more.
2. There's a good deal of explanation in the paper about the overestimation bias of Q-learning and how the Double Q-learning algorithm addresses this issue. I didn't cover it in depth in this post, but it's worth reading about if you're interested in reinforcement learning theory.
3. There were multiple games where the regular DQN algorithm's overestimation bias directly correlated with poor performance, such as Wizard of Wor and Asterix. In these cases, once the value estimates started exploding, the algorithm was unable to recover. The Double DQN algorithm was able to mitigate this issue and converge to a stable solution. I think these cases are particular interesting because it shows that overestimation doesn't just lead to suboptimal performance, but can actually lead to catastrophic failure in some cases.