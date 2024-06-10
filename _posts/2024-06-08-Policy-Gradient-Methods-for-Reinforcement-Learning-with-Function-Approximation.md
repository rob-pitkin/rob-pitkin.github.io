---
layout: post
title: A Paper a Week - Week 18
subtitle: ""
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/PolicyGradientMethods.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week18, paper]
---

### Introduction
For the eighteenth post in this series, I read "Policy Gradient Methods for Reinforcement Learning with Function Approximation" by Sutton et al. This paper presents the famous "policy gradient theorem" which is the basis for policy gradient methods in reinforcement learning. The authors show that the gradient of a differentiable policy can be estimated from experience aided by "an approximate action-value or advantage function". This result is significant because it proves that policy iteration with arbitrary differentiable policies is possible and convergent, which was not previously known.

### Summary
Unlike value-based methods, which estimate the value of a state or state-action pair via a parameterized function approximator, policy gradient methods directly parameterize the policy and optimize it with respect to a policy objective function with gradient ascent (or another gradient-based optimization method). As such, these methods model the policy directly, that is, the probability of selecting an action given a state, rather than using a greedy or epsilon-greedy policy to select actions.

When a policy is modeled this way, the goal becomes to optimize the parameters, say $$\theta$$, given a policy $$\pi_{\theta}(s,a)$$. In order to do so, one needs to measure the performance of the policy, typically with a policy objective function. There are two main policy objective functions presented in the paper; the first is the "average reward" objective function, which is the expected reward per time step, and the second is the "average reward per start" objective function, which is the expected reward per episode. The former is used for continuing tasks, while the latter is used for episodic tasks. Given these objective functions, in order to update the policy parameters, one needs to compute the gradient of the objective function with respect to the policy parameters, $$\theta$$:

$$\delta\theta = \alpha\frac{\partial J(\theta)}{\partial\theta}$$

where $$\alpha$$ is the step size and $$J(\theta)$$ is the objective function. The authors show that this gradient can be estimated from experience using the policy gradient theorem, which states that the gradient of the objective function can be expressed as:

$$\nabla_{\theta}J(\theta) = \sum_{s}d^{\pi}(s)\sum_{a}\nabla_{\theta}\pi_{\theta}(s,a)Q^{\pi}(s,a)$$

where $$d^{\pi}(s)$$ is the stationary distribution of states under the policy $$\pi$$, and $$Q^{\pi}(s,a)$$ is the action-value function under the policy $$\pi$$. Another way of expressing this gradient is:

$$\nabla_{\theta}J(\theta) = E_{\pi}[\nabla_{\theta}log\pi_{\theta}(s,a)Q^{\pi}(s,a)]$$

or the expected value of the gradient of the log policy times the action-value function. The proof of this is non-trivial and relies on the likelihood ratio:

$$\nabla_{\theta}log\pi_{\theta}(s,a) = \frac{\nabla_{\theta}\pi_{\theta}(s,a)}{\pi_{\theta}(s,a)}$$

However, $$Q^{\pi}(s,a)$$ is not always available, so the authors propose using an approximate action-value or advantage function, $$\hat{Q}(s,a)$$, to estimate the gradient. The authors prove, with the help of the compatible function approximation theorem, that as long as the approximate action-value function is compatible with the policy, using it to estimate the gradient is exact. Lastly, the authors present the third theorem of the paper, the policy iteration with function approximation theorem, which states that policy iteration with arbitrary differentiable policies is possible and convergent.

Altogether, the policy gradient theorem, the compatible function approximation theorem, and the policy iteration with function approximation theorem provide a theoretical foundation for policy gradient methods in reinforcement learning. These methods have better convergence properties than value-based methods, are effective in high-dimensional or continuous action spaces, and can learn stochastic policies.

### Questions/Notes I Have
1. Here's a link to the [paper](https://proceedings.neurips.cc/paper/1999/file/464d828b85b0bed98e80ade0a5c43b0f-Paper.pdf) for anyone curious about learning more.