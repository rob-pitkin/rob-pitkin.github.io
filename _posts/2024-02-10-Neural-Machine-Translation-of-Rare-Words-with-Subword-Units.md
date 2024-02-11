---
layout: post
title: A Paper a Week - Week 6
subtitle: "Neural Machine Translation of Rare Words with Subword Units, Sennrich et al."
cover-img: /assets/img/apaperaweek.jpg
thumbnail-img: /assets/img/BPE.jpg
share-img: /assets/img/apaperaweek.jpg
tags: [week6, paper]
---

### Introduction
For the sixth post in this series, I read "Neural Machine Translation of Rare Words with Subword Units" by Sennrich et al. The primary idea outlined in the paper is to use byte pair encoding to compress vocabularies and improve performance on the NMT task (translating sentences from one languange to another). You can imagine the two extremes of vocabularies for an NMT task are to either use a unigram character-level vocabulary, or to use only full words that appear in the training set, which is similar to using a dictionary. Byte pair encoding optimally splits the difference between these two extremes by breaking down larger words into subwords (typically bigram or trigram length units) based on the most frequently occuring subwords. In doing so, one can set a specific desired vocabulary size and then use the most frequent subwords to compress a larger vocabulary to the desired size.

### Summary
The motivating problem of the paper is that rare words are typically hard to translate in the NMT task. This is due to the word's low frequency in the training set, which makes it hard for models to translate correctly or even at all (in the case of a completely new word, such as a name). Other known approaches include creating new bigram vocabularies that only include pairs of two letters generated from the training set for each language. These perform relatively well but create very large vocabularies and don't choose subwords units optimally at all. For example, a bigram would be generated for a pair of characters that's only seen once in the training set and not a single time in the dev or test set. Furthermore, bigram models can perform poorely on translating large words or phrases due to the finer granularity that a bigram creates relative to word-to-word translation or subword-to-subword translation. The two NMT tasks that the authors chose for their experiments were English to German translation, where the two languages share an alphabet (for the most part), and English to Russian, where the two languages don't share an alphabet. In the Russian case, the authors performed extra steps where they transliterate the Russian vocabulary to latin characters and then perform the byte pair encoding and then transliterate the results back to Cyrillic in order to apply them to the Russian text. Additionally, the authors used the Bahdanau et al. attention encoder-decoder network from Week 2's paper to experiment with different encodings.

As I alluded to in the summary, the BPE algorithm relies on subword frequencies to create optimal subword units. The algorithm itself is split up into two steps, generating the most frequent pair of tokens and then performing a merge in the vocabulary to replace the most frequent pair of tokens with a single token. The procedure of the algorithm is as follows:
1. Take the vocabulary of words and create a frequency dictionary from the training set
2. For each word in the frequency dictionary, split it into each of its characters
3. Create a new token frequency dictionary and then iterate over each pair of characters in the word left to right and add it along with the word's frequency into the new dictionary. Continue doing this using the same dictionary for each word.
4. Return the most frequent pair of characters encountered.
5. The merge step then replaces the character level pair with the newly merged most frequent token (same pair of characters, just treated as a single token now)
6. Steps 2-5 are repeated with the updated frequency dictionary except 'characters' is now replaced with 'tokens' where a token could be either a character or a merged token. These steps are continued until a set number of merges has occurred or the vocabulary sized has condensed to a desired size

Since the steps are a bit confusing, I'll share an example given in the paper:
1. Given the vocab {'low', 'lower', 'newest', 'widest'}
2. Each word is broken down into its characters: {'l o w', 'l o w e r', 'n e w e s t', 'w i d e s t'}
3. The following merges are done: l + o -> lo, lo + w -> low, e + r -> er, e + s -> es, es + t -> est, along with others

As you can see, the resulting merged subword units can be of variable length and are based off of the frequency of the subwords in the dataset rather than using a blanket procedure like the bigram approach. You can imagine the subwords: new, low, _est, and _er can generate the words low, lowest, lower, new, newest, newer by using two fewer tokens than the resulting vocabulary. Additionally, the authors found that the resulting subwords result in better language-to-language translations since the same subwords, albeit in each's respective language, are used in the same way across both languages.

The BPE algorithm is most popularly used with the GPT series of models as well as the RoBERTa series of models, proving its effectiveness and superiority over other approaches. The results of the paper, albeit outdated, proved that BPE outperformed unigram, bigram, and word-level approaches on both english to german and english to russian tasks.


### Questions/Notes I Have
1. For a much better explanation of BPE, as well as code block examples, I recommend this [article](https://d2l.ai/chapter_natural-language-processing-pretraining/subword-embedding.html#byte-pair-encoding).
2. I think the best part of BPE is that it's statistically motivated based on actual token frequency. This not only results in better models but also reveals insights into how language vocabularies can best be understood and represented.