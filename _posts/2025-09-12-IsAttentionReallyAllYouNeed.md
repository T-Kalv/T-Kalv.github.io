---
title: 'Is Attention Really All You Need?'
date: 13/09/2025
permalink: /posts/2025/09/2025-09-13-IsAttentionReallyAllYouNeed/
tags:
  - Transformers
  - Paper Summary
  - Research
  - Artificial Intelligence
  - Machine Learning
  - Deep Learning
  - Neural Networks
---

In 2017, Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uzkoreit, Llion Jones, Aidan N. Gomez, Lukasz Kaiser and Illia Polosukhin published the Research Paper ***"Attention Is All You Need"***, which introduced the ***Transformer*** model architecture, but is Attention really all you need? In this post, I'll summarise the key ideas in this Research Paper and share my takeaways of whether or not "Is Attention Really All You Need"?

## Background
### Why Transformers?
Before 2017 of the introduction of the Transfomer, the majority of sequence-to-sequence models relied on Recurrent Neural Networks (RNN) or Long Short-Term Memories (LSTMs). These models were quite slow and they processed words sequentially, one by one and they often struggled with longer sentences between tokens. This lead to the authors of the paper *"Attention Is All You Need*, proposing a different approach with an Attention Mechanism.

Attention is a mechanism that Transformers use that allows the model to look at all the words at once and decides how much each word should influence the other. Then it combines the most relevant words to understand meaning, a concept called Self-Attention. This results in a model that captures long-range relationships in text while being much faster/more parallelisable than previous methods such as Recurrent Neural Networks (RNN) or Long Short-Term Memories (LSTMs).

## Core Ideas
### Self-Attention:
Instead of processing words sequentially, one by one, the model creates three vectors for each token which are:
- Query (Q)
- Key (K)
- Value (V)

Then it multiplies the input embedding by the weight matrices that have been previously trained/learned. Next it computes the Attention scores between one token's Query and the other tokens' Values to decide how much each word should influence the others. Then a Softmax function is used where the Attention scores are passed through the function to normalise them into probabilities. Then the model uses these probabilities to take the weighted sum of Value Vectors which then produces a new representation for each token that uses the information from the other tokens. This Self Attention mechanism allows the Transformer to work with long-range relationships in the text. This Self-Attention is computed as:
$$
\text{Attention}(Q,K,V) = \text{softmax}\!\left(\frac{QK^{T}}{\sqrt{d_k}}\right)V
$$
where $K^{T}$ is the transpose of the Key Matrix and $d_k$ is the dimensionality of the Key Vectors.

### Positional Encoding:
As Transformers process words in parallel instead of sequential, they lose the natural sequential order of the original text which is why the authors introduced Positional Encoding where they introduced adding information about the positions of each of the words in the sequences. The Positional Encoding is computed as:
$$
PE_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d_{model}}}\right), \quad
PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d_{model}}}\right)
$$
where:
- $pos$ is the token position in the sequence
- $i$ is the index dimension
- $d_{model}$ is the embedding size

### Multi-Head Attention:
Multi-Head Attention which is similar to Self-Attention, allows the model to look at long-range relationships from multiple perspectives simulataneously. Here instead of computing the vectors Query (Q), Key (K), Value (V) as a single set, the Transfomer computes heads of the Query, Key, Value Matrices. Each head has its own learned projection Matrices so that the same input is interpreted in different ways, allowing the model to capture the diverse relationships between the words in the text. 

For each head, the attention is computed as:
$$
\text{head}_i = \text{Attention}(Q W_i^Q, K W_i^K, V W_i^V)
$$ 
Then all heads are concatenated and projected:
$$
\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h) W^O
$$

### Feed-Forward Network (FFN):
After the Attention layers, each Transformer layer has a Feed-Forward Network (FNN) that helps the model transform its representation in a non-linear way. The Feed-Forward Network (FFN) is computed as:

$$
\text{Feed-Forward Network}(x) = \text{max}(0, x W_1 + b_1) W_2 + b_2
$$
where the weight matrix $W_1$ transforms the input vector.

### Encoder:
The Encoder stack composes of a stack of $N$ indentical layers where each layer contains a multi-head self-attention sublayer and a position-wise fully connected feed-forward network with residual connections as well as layer normalisation. The output of each sub-layer is computed as:
$$
\text{LayerNorm}(x + Sublayer(x))
$$

### Decoder:
The Decoder stack also composes of a stack of N identical layers where it has the same two sub-layers as in each encoder layer except the Decoder has an additional third sub-layer where it peforms multihead attention over the encoder's output. In addition, the Decoder stack contains a masked multi-head self attention sub-layer that prevents positions from peaking at future tokens instead of the normal multi-head self-attention sub-layer found in the Encoder stack.

## Tranformer Architecture:
![Transform Architecture](/_posts/Transformer.svg)

*Figure 1. Transformer Architecture. Adapted from Vaswani et al. (2017).*

## Results:
The Research Paper ***"Attention Is All You Need"*** showed that the ***Transformer*** model architecture achieved state-of-the-art results where their model ***"achieves 28.4 BLEU on the WMT 2014 English-to-German translation task"*** and on the ***English-to-French*** translation task their model achieved a ***"new state-of-the-art BLEU score of 41.8 after training for 3.5 days on eight GPUs"***. This demonstrated that the Transformer architecture can output recurrent and convolutional models while training much faster.

## My Takeaways:
After reading ***"Attention Is All You Need"***, we can see why the Transfomer plays such a pivotal role in Natural Language Processing (NLP) and Large Language Models (LLM) with having real-world impact in modern applications such as ChatGPT (Decoder-only Transformers), BERT (Encoder-only Transformers), Google Translate (Encoder And Decoder)...

For me, the key takeways were:
- **Multi-head Attention** - allows the model to see complex relationships from multiple perspectives
- **Positonal Encoding** - adds information about the order of the words in the sentence
- **Parallel Processing** - allows faster training as no sequential bottleneck
- **Feed-Forward Layers** - gives each token its own small-linear transformation after attention
- **Scalability** - powers poweful models across text, images, audio...


## Further Reading:
- [Original paper: *Attention Is All You Need* (Vaswani et al., 2017)](https://arxiv.org/pdf/1706.03762)
- [3Blue1Brown Video: *Transformers, the tech behind LLMs | Deep Learning Chapter 5*](https://youtu.be/wjZofJX0v4M?feature=shared)
- [3Blue1Brown Video: *Attention in transformers, step-by-step | Deep Learning Chapter 6*](https://youtu.be/eMlx5fFNoYc?feature=shared)
- [Caelb Writes Code Video: *Transformers Explained*](https://youtu.be/nZrZOI0oRuw?feature=shared)
- [Wikipedia Article: *Attention Is All You Need*](https://en.wikipedia.org/wiki/Attention_Is_All_You_Need)
