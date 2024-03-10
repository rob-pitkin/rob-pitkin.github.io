---
layout: post
title: A Paper a Week - Week 10
subtitle: "Human-level control through deep reinforcement learning, Mnih et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/DQN.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week10, paper]
---

### Introduction
For the tenth post in this series I took a step back in the field of RL and decided to start with one of the seminal papers of deep reinforcement learning, "Human-level control through deep reinforcement learning", by Mnih et al. This paper presents the Deep Q-Network architecture/algorithm, which was the first established architecture for applying deep learning to the traditional Q-learning algorithm.

### Summary
The primary idea presented in this paper was creating a novel deep learning approach to achieve state of the art performance on 49 Atari games. The approach centered around three significant novelties; the first being the convolutional architecture used for processing game inputs, the second being the dataset randomization done during training coined as "experience replay", and the third being all of the training and network design done to enable efficient and effective learning.

The authors defined the states of the Atari games as the individual video frames being shown by the emulator, the current score, and, if applicable, the lives remaining in the game. With this as the input, the authors also perform additional preprocessing steps such as compressing the original frames from a size of 210 x 160 x 4 to 84 x 84 x 4 and considering every two frames as a single state due to how Atari alternates in rendering certain objects (i.e. renders some things every odd frame and others every even frame). The network itself consists of two convolutional layers with ReLU activations in between them followed by two fully connected layers, also separated by a ReLU activation. Finally, there's a fully connected output layer where each of the N outputs represents a different action that can be performed and the value of the output unit represents the Q-value of performing that action given the current state. The loss or objective function for the network is then the modified Q-learning update function:
$$L_i(\theta_i)=\mathbb{E}_{(s,a,r,s')\sim U(D)}[(r+\gamma\text{max}_{a'}Q(s',a';\theta_i^-)-Q(s,a;\theta_i))^2]$$

where $\theta$ represents the network parameters, $s,a,r,s',\text{and} a'$ are the current state, the current action, the current reward, the next state, and the next action. $\sim U(D)$ means the samples are drawn uniformly from the dataset and $\theta_i^-$ represents a fixed state of the network that is updated with the current parameters at iteration $i$ every $C$ timesteps. This enables the algorithm to strike a balance between online learning (would update after every timestep) and offline learning (would update after an entire trajectory, which doesn't apply in this case because we are drawing the samples randomly). The $r+\gamma\text{max}_{a'}Q(s',a';\theta_i^-)$ portion of the loss is the modified Bellman equation where in this case the authors are substituting $Q^*(s,a)$ with $Q(s,a;\theta)$ (i.e. the deep Q-network).

The second novelty, experience replay, comes into play with regards to actually training the network. I mentioned it briefly above, but the technique consists of storing each of the agent's experiences $e_t=(s_t,a_t,r_t,s_{t+1})$ pooled over many episodes into a dataset $D={e_1,...,e_t}$. This dataset has a max capacity of $N$ and then during training is sampled from uniformly to apply Q-learning updates. This approach has the advantage of breaking up the high correlation between time-consecutive samples which enables more effective updates and less variance. As a result, the learning process isn't dominated by feedback loops with particular actions. For example, if the best action was to move left, all of the subsequent updates will have the bias of have moving left during the initial timestep. Experience replay averages this behavior influence across all experiences so that it's not disproportionally felt during a sequence of updates. Lastly, also as I mentioned earlier, the authors store a $\theta_i^-$ every $C$ timesteps to serve as a "target network" which to compare the current state of the network against. This approach has the effect of reducing oscillations or divergent behavior since the target network is temporarily fixed rather than being updated in an online fashion.

The results of the paper show that the DQN approach beat state of the art methods across 43 of the 49 atari games and achieved human-level performance on 29 of the 49 games, proving just how effective the DQN algorithm is and how well it can generalize to different problems.

### Questions/Notes I Have
1. I'm going to re-post the good visual [article](https://towardsdatascience.com/reinforcement-learning-explained-visually-part-5-deep-q-networks-step-by-step-5a5317197f4b) on Deep Q Networks, which was one of the original uses of deep learning in RL and will set the stage for some papers to come.