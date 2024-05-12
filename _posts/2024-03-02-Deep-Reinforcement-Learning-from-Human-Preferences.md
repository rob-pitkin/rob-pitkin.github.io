---
layout: post
title: A Paper a Week - Week 9
subtitle: "Deep Reinforcement Learning from Human Preferences, Ouyang et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/drlFHP.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week9, paper]
---

### Introduction
For the ninth post in this series I initially wanted to read the paper on RLHF in relation to LLMs, as I've been seeing it in more and more papers as a leading method of fine-tuning LLMs for human preferences. However, in the process of reading this paper I quickly realized I didn't understand a lot of the RL methods being used and ended up bibliography hopping a bit to get to the actual paper for this week, "Deep Reinforcement Learning from Human Preferences" by Ouyang et al. Although, even in this paper there's still a good deal that I need to build up my RL foundation a bit more to understand, the high level approach was easy to grasp and a good stepping stone towards merging the two subject matters (LLMs and RL).

### Summary
I plan on taking the next couple of weeks to do more of a deep dive on RL basics, so forgive me if this post is a bit all over the place. The high level approach of this paper was to create an RL architecture such that using human feedback to train RL systems could be scaled to modern deep RL problems. While a variety of tasks were chosen to evaluate the authors' approach on, the two main environments was the Atari game environment (contains a collection of basic atari games) and the MuJoCo physics simulator (contains a variety of physics-based control tasks). The general architecture of the RL algorithm involved two main components, the reward estimator and the policy function, each of which was parameterized by a deep neural network. 

The reward estimator is trained via online (read real-time) feedback from a non-expert human. This feedback comes in the form of preferences rather than a numerical score or evaluation. This is partially due to the advancement of preference-based policy gradient methods and partially due to how it's easier for humans to compare two trajectories (read sequences of actions by the agent) than to provide an accurate estimate for a score. These preferences were assigned by creating a dataset of trajectories by the agent performing each task and then splitting the trajectories into segments and pairing the matching segments across trajectories. With these trajectory segment pairs (1-2 second video clips), the human can then choose which one they prefer, if they are the same in evaluation, or if they have no preference. This online human preference feedback process ranged anywhere from 30 minutes to 5 hours in some cases. 

The policy network is then trained via any traditional RL algorithm by maximizing the rewards generated by the reward estimator given a particular action and observation (also referred to as action and resulting state in more traditional RL literature).

I won't get into the math too deeply in this post as I don't feel confident in my ability to explain it well due to my shakiness on the RL methods presented in this paper, but I will summarize the results. The authors experimented with online feedback from a real non-expert human on each of the tasks in both environments, but also created an "oracle" (always chooses the trajectory segment that has a higher reward) to query for preference at a variety of levels, both less than and greater than the amount sourced from actual humans. The authors found that simply using real human feedback (~750 labels for MuJoCo tasks and 5.5k labels for Atari tasks) they could compete or even beat state of the RL methods that don't use human feedback. Additionally, the synthetic feedback, when scaled up to higher values than the human levels (1400 for MuJoCo and 10k for Atari) consistently outperform SOTA RL methods on MuJoCo tasks and tie or outperform SOTA methods on a few Atari tasks.

The main takeaway for this paper is that sourcing human feedback and training an RL system with it, which is a form of cooperative inverse reinforcement learning, is in many cases easier to come by than to create extremely high quality reward functions for traditional RL methods. By showing that this methodology could be scaled up to deep RL approaches, it proves its viability on more complex tasks where the reward function isn't trivial, such as control tasks.

### Questions/Notes I Have
1. I'm planning on starting this [RL Coursera course](https://www.coursera.org/learn/dmrol#modules) to refresh my fundamentals on RL and would recommend it if this post seemed interesting to you.
2. Here's a good [article](https://towardsdatascience.com/reinforcement-learning-explained-visually-part-5-deep-q-networks-step-by-step-5a5317197f4b) on Deep Q Networks, which was one of the original uses of deep learning in RL and will set the stage for some papers to come.