---
layout: post
title: A Paper a Week - Week 8
subtitle: "Fine-Tuning or Retrieval? Comparing Knowledge Injection in LLMs, Ovadia et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/finetuningVsRag.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week8, paper]
---

### Introduction
For the eighth post in this series, I read "Fine-Tuning or Retrieval? Comparing Knowledge Injection in LLMs" by Ovadia et al. Following last week's paper on RAG as a knowledge injection technique, I found this study done comparing RAG with the more traditional task-specific fine-tuning. Surprisingly, the results of the study assert that RAG outperforms unsupervised fine-tuning essentially across the board, which further proves its legitimacy as a premier knowledge injection/updating technique for LLMs.

### Summary
Knowledge, at least for LLMs, is defined by the study as the ability to accurately answer factual multiple choice questions (otherwise known as a version of the question answering task). That is, given a set of questions and a set of answers, a model is deemed to have any knowledge if its accuracy is greater than $\frac{1}{L}$, where $L$ is the number of choices per question (i.e. better than guessing randomly). As a result, the main evaluation metric being used for the approaches in the study is extremely factual-based, as opposed to logical reasoning or critical thinking, which they acknowledge as an imperfect approach, but a valid one nonetheless.

Since the focus of the study is factual knowledge, the authors provide five main ways in which a model can encounter factual errors:
1. Lacking domain knowledge - For example, an LLM trained on exclusively english would perform poorly on another language.
2. Outdated information - This was one of the primary motivating factors for RAG, but essentially this means due to the training data being capped at a particular date and time of training, the model hasn't been trained on newer information.
3. Failure to memorize information - During the training process, models can fail to memorize facts or knowledge it's only seen relatively few times.
4. Forgetting - There's a phenomenon that exists during fine-tuning known as catastrophic forgetting, where models will lose knowledge they held during pre-training due to fine-tuning on task-specific data.
5. Reasoning failure - In this case, the model possesses the correct knowledge, but just fails to utilize it correctly due to more complex or multi-step reasoning processes that need to be done to use the knowledge effectively.

With the above sources of errors, the primary goal of the study is to find some model transformation, denoted as $F$ in the paper, such that given a base model, $M$ and a relevant auxiliary knowledge base, $B_Q$, (either used for fine tuning or retrieval) the output of the transformation $M'$ has a higher accuracy score on the factual-based question answering task described earlier:
$$M'=F(M,B_Q)$$

The transformations suggested by the study are unsupervised fine-tuning and retrieval augmented generation. The former is essentially a continuation of pre-training except with task specific data (such as a question answering dataset) and the latter, as discussed last week, involves extending the LLM architecture with a Retriever and an auxiliary knowledge base represented as a dense embedding vector store such that "relevant information" can be queried by the retriever and concatenated to the model input to provide additional context/knowledge.

The two approaches, along with a combined FT + RAG approach, was evaluated on two main benchmarks. The first is the MMLU benchmark, which stands for Massively Multilingual Language Understanding Evaluation, and involves he topics of anatomy, astronomy, college biology, college chemistry and prehistory. The second benchmark was a custom "current events" dataset created by the authors which consisted of current events and news in the USA scraped from Wikipedia from Aug-Nov 2023 (guaranteeing the base model had never seen it). The results showed that on both benchmarks, RAG alone outperforms both fine-tuning and a combination approach across the board. There wasn't a single result that showed fine-tuning as a superior approach and only a minority of results showed the combination approach as tieing or beating out RAG by itself.

### Questions/Notes I Have
1. I'm choosing to reuse my resource from last week since this paper didn't necessary establish any new architectures or methods - For a visual explanation of RAG models, I recommend this [article](https://www.superannotate.com/blog/rag-explained).
2. Although this study used a relatively constrained notion of knowledge (factual question accuracy), I do think it points to a larger notion that RAG as a knowledge injection approach is extremely promising approach that will hopefully be built upon as massive LLMs require knowledge refreshing in the coming years.