---
layout: post
title: A Paper a Week - Week 7
subtitle: "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks, Lewis et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/rag.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week7, paper]
---

### Introduction
For the seventh post in this series, I read "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" by Lewis et al. Otherwise known as "RAG", retrieval-augmented generation refers to the idea of providing sequence-to-sequence models with an up to date knowledge base that can be leveraged to answer questions or accomplish tasks more accurately and factually than a standard pre-trained or fine-tuned model can. RAG directly addresses the problem of models being limited by the data they are trained on, such as not being able to answer "who won the 2024 Super Bowl", since all of the training data is from 2023 or earlier.

### Summary
While the paper uses a lot of technical language and is very exact in explaining the probablistic modeling that RAG leverages, the higher level idea of RAG is pretty simple. The general architecture is split into two components, a Retriever and a Generator. The Retriever's purpose is to take a query representation and retrieve the top-k documents related to the query from a document index. These k documents are then concatenated with the query representation to provide additional and up to date context and fed into the Generator to generate an answer or a task output. The main motivation for RAG is the idea that instead of having to repeatedly fine-tune a model on more and more up to date data, which is very expensive, one can just replace or refresh the retriever to provide up to date context. It separates the notion of a model's memory with its ability to understand and process language (or sequential data). Furthermore, with the addition of a retriever being able to add additional helpful context to a query, a RAG model can easily outperform its finely tuned counterparts.  

The architectures used for RAG models are split up by the parametric portions (the generator and query encoder) and the non-parametric portion (the retriever). What this really means is that during training, the parameters of the retriever remain fixed and the fine tuning of a RAG model only updates the parameters of the query encoder and generator. This helps RAG models stay computationally proportional to standard seq2seq models since the expensive back propagation steps only apply to portions that are commonly found in traditional seq2seq models. The Generator is any seq2seq model, but in the case of this paper, BART was used which incorporates a standard transformer architecture with a bidirectional encoder and a unidirectional decoder and a particular emphasis on denoising during pre-training. The Retriever can be modeled by any function that accepts a query representation and can return the top-k documents relevant to said query, but in the case of the paper, a pre-trained BERT model was used in combination with a dense vector index of Wikipedia. The BERT model was trained on specifically retrieving document vectors related to question inputs via trivia and natural question datasets. However, as I mentioned earlier, this BERT model's parameters are fixed during the training of the larger RAG model and it's treated as a static document retrieval model (a.k.a additional "memory" for the model).

To make the forward propagation of a RAG model a bit more clear, I'll use my "who won the 2024 Super Bowl" query as an example:
1. the query text would be fed into the query encoder model which would create a high-dimensional embedding representation for the query
2. the query representation would be fed into the retriever model (BERT + dense document vector index of Wikipedia) to return the top-k document representations that can help answer the query (or are similar to the query)
3. the top-k document representations (question context) are then concatenated with the query representation and fed into the generator (seq2seq model, BART)
4. the generator generates the next token in the sequence based on the input and the previous tokens generated so far (if any) by marginalizing the output (essentially taking the most likely one).

The authors of the paper outlined two strategies for decoding within the generator portion - sequence level and token level. In the former's case, a single document is used to generate every token in the decoded/generate output sequence. This means that for each of the top-k documents, only that context concatenated context is used for the entire output sequence. In the latter's case, each of the top-k documents can be used for every output token. In other words, the document that provides the highest output token probability is used for that particular decoding step. While both strategies are viable, there are performance and consistency tradeoffs in either case.

RAG models have become extremely popular as of late due to how costly it can be to finetune massive LLMs on more up to date training data. Instead, one can just swap out a much smaller retriever with a more up to date document index and don't have to touch the larger generator model at all. This strategy and architecture has been found to increase performance, even beating state of the art models, on a variety of knowledge-dependent tasks. However, what I find more intriguing about RAG models is the notion of separating a model's knowledge base out from it's task performing portions. This way of thinking is more akin to constructing different portions of the brain rather than treating it as an indivisible entity.


### Questions/Notes I Have
1. For a visual explanation of RAG models, I recommend this [article](https://www.superannotate.com/blog/rag-explained).