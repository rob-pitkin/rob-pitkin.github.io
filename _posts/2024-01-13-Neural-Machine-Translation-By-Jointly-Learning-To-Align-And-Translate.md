---
layout: post
title: A Paper a Week - Week 2
subtitle: Neural Machine Translation by Jointly Learning to Align and Translate, Bahdanau et al.
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/NeuralMachineTranslation.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week1, paper]
---

### Introduction
For the second post in this series, I read the Bahdanau et al. 2014 paper, "Neural Machine Translation by Jointly Learning to Align and Translate", which is credited with the first use of "attention" for sequential models. I use quotation marks around the word attention because the mechanism used in the paper is quite different in terms of mathematical implementation from the scaled dot product attention (sometimes known as QKV attention) that is used in transformers and in practice today for any form of attention due to its computational efficiency. That said, the high-level concept of using information from each one of the sequential inputs from the encoder to help predict the next output of the decoder was indeed born in this paper and its this concept which gave birth to the mechanism used in week one's paper, "Attention is all you need", by Vaswani et al.

### Summary
The motivating problem for Bahdanau Attention, the attention mechanism defined in the paper, was to address the information bottleneck caused by using a fixed length vector to encode source input for the task of machine translation using a traditional encoder-decoder architecture (RNN, LSTM, etc.). Since inputs, typically sentences in this paper, are of variable length, using a fixed length vector to try and encode information about a variable length input is by definition limiting in terms of how much information can be encoded or translated to the decoder.

To combat this problem, Bahdanau et al. employ an attention mechanism called the "alignment model" that takes as input the hidden state of each input of the encoder as well as the previous timestep's decoder hidden state and feeds them all into a FFN, which outputs a matrix of alignment (attention) weights for each of the hidden states (one alignment vector per hidden state). Then, to finally get the context vector to pass to the decoder, they multiply each alignment vector with its corresponding encoder hidden state and sum them all up. In this way, the context vector will contain information relevant (via alignment) to the current word being predicted, rather than just having the entire input sentence encoded without any weighting. The equations for this mechanism are as follows:

* Calculating the alignment scores (FFN operations):
$$
\large e_{ij} = v_a^\top \tanh{\left(W_a s_{i-1} + U_a h_j\right)}
$$
where $v_a$, $W_a$, and $U_a$ are all learnable weight matrices, $s_{i-1}$ is the decoder hidden state from the previous time step, and $h_j$ are the encoder hidden states for each time step. The first layer's forward computation is implemented by concatenating $W_a$ with $U_a$ and concatenating the K encoder hidden states with K copies of the previous timestep's hidden state and then multiplying these two matrices together (represented by the sum inside the tanh in the above equation). The tanh activation followed by the matrix multiplication with $v_a$ represents the second layer in the FFN. The output is then the matrix of alignment scores, which is turned into weights in the following step.

* Transforming the alignment scores into alignment weights (softmax):
$$
\large \alpha_{ij} = \frac{\exp{\left(e_{ij}\right)}}{\sum_{k=1}^K \exp{\left(e_{ik}\right)}}
$$

* Creating the context vector:
$$
\large c_i = \sum_{j=1}^K\alpha_{ij} h_j
$$

Thus, a context vector is generated for each timestep of the output from the decoder, which it then uses to predict the next word in the sentence using standard inputs of the previous predicted word, the previous decoder hidden state, and this context vector. This mechanism can be utilized for unidirectional and bidirectional RNN's, where each encoder direction would compute their own hidden states and those would be concatenated to form the hidden states in the above equations, $h_j$.

The authors of the paper argue that the mechanism enables the a way to encode relevant information about surrounding words rather than having to encode the entire input sentence (i.e. look at close neighbors in the source sentence to determine next word in the output). However, this mechanism can also assign weight to parts of the input that are far from the current word, since each encoder hidden state is assigned its own weight. This long-distance attention capability is the basis for all transformers and is what makes them so powerful relative to traditional sequential models.

### Questions/Notes I Have
1. The equation for scaled dot product attention, the attention currently dominating transformers, is as follows:
$$ 
\large \mathrm{Attention}\left(Q, K, V\right) = \mathrm{softmax}\left(\frac{QK^{\top}}{\sqrt{d_k}}\right)V
$$
where $Q$, $K$, and $V$ are matrices of N vectors, one for each of the N input tokens, and they are found by multiplying an embedding vector (after applying a positional encoding step, a la last week) with a corresponding query, key, or value matrix ($W_q$, $W_k$, $W_v$), all of which are learned. The output of the softmax operation can be thought of as the attention weights for each input token, whereas the matrix multiplication with the value matrix $V$ can be thought of as analogous to creating the context vector by multiplying the alignment weights with the corresponding hidden states and summing.
2. Some great resources for the Bahdanau paper that definitely explain it better than I do is this [article](https://machinelearningmastery.com/the-bahdanau-attention-mechanism/) from Machine Learning Mastery (which I highly recommend), and of course, this [article] from Dive Into Deep Learning, which is my go-to for all things deep learning.