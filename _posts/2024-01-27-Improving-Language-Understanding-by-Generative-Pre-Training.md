---
layout: post
title: A Paper a Week - Week 4
subtitle: Improving Language Understanding by Generative Pre-training, Radford et al.
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/gpt2.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week4, paper]
---

### Introduction
For the fourth post in this series, I read "Improving Language Understanding by Generative Pre-training" by Radford et al. It felt apt to read the GPT-2 paper, which set the foundation for the most popular transformer in use today, after learning about all of the transformer components individually the past three weeks (architecture, attention, embeddings).  

### Summary
The motivation behind the GPT family of architectures was to take advantage of the incredibly large sources of unlabeled data to initialize and develop a model's understanding of complex language and then fine-tune the model on a much more directed task with labeled data. This method is very similar to fine-tuning done in computer vision with very established architectures (U-Nets, ResNets) that are pre-trained on massive generalized datasets (i.e. ImageNet) and then fine-tuned on small custom datasets for specialized use-cases. However, in the case of GPT, the fine-tuning doesn't restrict the model to a single use-case nearly as much as it does for CV tasks due to the nature of NLP and the transformer's ability to generalize extremely well. The intuition to this approach is essentially that LLMs can learn a lot of complex relationships and gain a very good generalized understanding of how language works just by ingesting tons of data, which can then be leveraged (or transferred) to several related tasks.

The GPT (Generative Pre-training) architecture consists of isolating just the decoder half of the original transformer architecture, pre-training it on the generic language modelling task with boatloads of unlabeled data, and finally fine-tuning it on several more specific tasks via smaller labeled datasets, such as textual entailment, sentence similarity, question answering, and more. The choice of using only the decoder instead of using only the encoder is purely due to the desired tasks to learn. The main difference between the two halves is that the encoder can see the entire input sequence at any given time whereas the decoder uses a left-to-right mask such that inputs are processed left-to-right (via stacking the same tokenized vector N times and using a lower triangular matrix of all 1s to mask tokens). This results in the attention heads only being able to attend to tokens before the current input "timestep" at any given time. Due to its left-to-right nature, this type of attention is also known as causal attention or causal self attention, instead of regular self attention. The specific architecture used by Radford et al. consisted of the traditional text embedding and positional embedding layers followed by twelve transformer decoder blocks, all of which only performing one instance of masked multi-headed self-attention since there isn't an encoder to receive input from, and finally a FFN which is added after the pre-training is done and is trained solely on the different tasks. In reality, these "decoder" blocks are actually much closer, if not identical, to the structure of the original transformer encoder blocks with the sole exception of the causal attention (left-to-right) mask.

The key ideas of the paper can be summarized with the three objective functions. The first objective function is the pre-training objective function, which uses the language modelling task (i.e. next word prediction). The equation is as follows:
$$L_1(U) = \sum_{i}\mathrm{log}P(u_i|u_{i-k},...,u_{i-1};\theta)$$

In this case, we are maximizing the log likelihood of the current token, $u_i$, from a corpus of unlabeled tokens, $U$, given the previous $k$ tokens, where $k$ is the size of the context window, by finding the conditional probability using a neural network with parameters $\theta$. With such a generic objective function, the model learns the relationships between words and how to generate the next word in a sequency by only attending to previous tokens.

The second objective function is the fine-tuning objective function, which uses a more specialized task with labeled data. The equation is as follows:
$$L_2(C) = \sum_{(x,y)}\mathrm{log}P(y|x^1,...,x^m)$$

where $y$ is the target label and $x^1,...,x^m$ are the input tokens. This objective function relies on labeled data, which is much harder to find large sources of, and is essentially the generic objective function of supervised learning on sequential data. Such an objective function can fit any number of supervised learning tasks since $y$ can be of any form (a class or even another sequence). In this case, we are maximizing the log probability of the target label $y$ given the input sequence $x^1,...,x^m$. 

While the above two objective functions alone would be enough to accomplish the pre-training and fine-tuning framework described, the authors added a third auxiliary objective function which combines the fine-tuning objective with a weighted pre-training objective term in order to improve model generalization and accelerate convergence. This final learning objective is modelled as follows:
$$L_3(C) = L_2(C) + \lambda * L_1(U)$$

This additional pre-training objective term is essentially a form of regularization on the fine-tuning objective function and the weight parameter, $\lambda $, is a hyperparameter that can be tuned on.

Overall, the GPT architecture represents an enormous step taken in the usability of LLMs, specifically transformers, due to how much can be learned by the architecture via pre-training. Since large unlabeled datasets are cheap relative to their labeled counterparts, leveraging them to create larger models with better performances that can also generalize to other downstream tasks opened the door to almost every kind of LLM you'd encounter today.

### Questions/Notes I Have
1. For an in-depth explanation of LLM pre-training, as well as a preview into next week's article, I recommend taking a look at this [article](https://d2l.ai/chapter_natural-language-processing-pretraining/word2vec.html), which has really helpful graphics and compares the decoder-only approach (GPT) with the encoder-only approach (BERT).
2. It's interesting to think about how GPT limits itself to a decoder-only architecture, which prohibits traditional self-attention and only uses causal self-attention. You'd think it's losing out on a lot of information that it could be using as context, but what it actually enables is the text generation task in the first place. By not relying on what comes after the "next word", it is able to predict the next word, whereas a "bidirectional" model, such as BERT, is restricted to a different set of tasks that require the whole context (more like text analysis rather than generation).