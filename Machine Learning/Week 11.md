
# Lecture 11 : Let's talk about text (and NLP)

### Mini recap : Bag of words (BOW)
Idea: For each sample, count the number of times each word in the dictionary appears. We build a dictionary from that and we can put it in a vector : 
![[Pasted image 20240507083654.png]]
This is fine for simple tasks (when the task is *at document level* ), but I don't help for : translation, co-reference resolution, text generation

## Word representations 

#### <u>Naive approach</u>
1.Define a vocabulary V
2.Each word in the vocabulary is represented by a sparse vector 
3.Dimensionality of sparse vector is size of vocabulary (thousands, millions !!)
![[Pasted image 20240507084052.png]]
***!!*** We don't have any similarity between common words ***!!***

#### <u>Word embeddings</u>
Represent each word as a high dimensional, **real-valued** vector
![[Pasted image 20240507084259.png]]
Similarity of vectors represents similarity of meaning for particular words.

The goal is to make a vector that links word with vectors (the same vector is going from man-->woman is "parallel" to king-->queen) (here in 3D but in reality in N dimensions)
![[Pasted image 20240507084427.png]]

<u>Training</u> 
**Supervised** 
- Optimize the embeddings for a specific task (e.g., sentiment analysis)
- Downsides: Requires supervised data, may not generalize to other tasks
**Unsupervised** (or self-supervised)
- “You shall know a word by the company it keeps”; J. R. Firth, 1957

<u>Context</u>
We can rely on the context in which words occur to learn their meaning 
	Context is the **set of words** that occur **nearby** 

*!!* But this technique has its limitation, for exemple the use of a positive word in a negative sentence

#### Sequence-to-sequence models 
Instead of modeling words, the standard approach consists of modeling **a complete sentence** or document as a sequence
	Note that each individual word is still modeled as a vector as discussed previously
![[Pasted image 20240507085201.png]]
But how do we model each block ?? => 

## Recurrent neural networks
**Solution** : Recurrent neural networks - NNs with feedback loops

Unrolling the RNN across all time steps gives full computation graph
![[Pasted image 20240507085447.png]]


### RNN for classification
When we want to predict only at the end of the sentence (like predict the sentiment of the sentence)
![[Pasted image 20240507085643.png]]

### RNN for sequence labeling 
We can also produce an output for each unit : 
![[Pasted image 20240507085708.png]]

### RNN for generation 
We can use it to (given the context) predict the next word
![[Pasted image 20240507085758.png]]
### But how does it work ? 
<u>Standard RNN</u>
$$h_t = σ(W_{hx}x_t + W_{hh}h_{t-1} + b_h) $$
$$z_t = σ(W_{zh}h_t + b_z)$$ 
![[Pasted image 20240507090404.png]]
<u>Long Short-Term Memory (LSTM)</u>
![[Pasted image 20240507090506.png]]
<u>Gated Recurrent Unit (GRU)</u>
![[Pasted image 20240507090517.png]]

#### The Training 
Backpropagate gradients through both the decoder and the encoder. 
![[Pasted image 20240507091914.png]]

#### Limitations 
State represented as a single vector —> Massive compression of information. 
Challenging to learn long-range dependencies/interactions. 


### Attention functions 
#### Incorporating attention 
Use the output of the Decoder LSTM to compute an attention (a mixture) over all the h outputs of the encoder LSTM. We want to focus on different parts of the input at each time step. 
![[Pasted image 20240507092326.png]]

#### The function 
**Compute** pairwise similarity between each encode hidden state and decoder hidden state 
![[Pasted image 20240507092423.png]]
**Examples :** 
<u>Multiplicative</u>
$$a = h^eWh^d$$
<u>Linear</u>
$$a = v^Tϕ(W[h^e;h^d])$$ <u>Scaled Dot product</u>
$$a = (Wh^e)^T(Uh^d)/d^{1/2}$$

Then we need to **convert** pairwise similarity scores to probability **distribution** (using softmax) over encoder hidden states and compute weighted average
![[Pasted image 20240507092925.png]]

### Back to our RNN 
![[Pasted image 20240507093109.png]]

We still have **1 problem** : encoder hidden states must be computed in series (not good for computation)

## Transformers
Our solution : **The transformer**
- Made up of encoder and decoder 
- Both encoder and decoder made up of multiple cascaded transformer blocks 
	- Slightly different architecture in encoder and decoder transformer blocks 
- Blocks generally made up **multi-headed attention** layers (self-attention) and **feedforward** layers
- No recurrent computations 
This give us this mess :
![[Pasted image 20240507093554.png]]

### Self attention 
Computed for each word, from all the others words
![[Pasted image 20240507093749.png]]
![[Pasted image 20240507093757.png]]

We can also make this ***MULTI*** 
#### Multi-head self-attention 
![[Pasted image 20240507094111.png]]

#### Masked multi-head self-attention 
Self-attention can attend to any token in space (like *future* word in the sentence)
For the decoder **you don't want tokens to attend to future tokens** 

=> Mask the attention score of future tokens so their attention = 0

#### Cross-attention 
Cross attention is the same classical attention as in the RNN encoder-decoder model
The query to the attention function is the output of the masked multi-headed attention in the decoder 
The keys and values are the output of the **final** encoder transformer 
Once again, a representation from the decoder is used to **attend** to the decoder outputs . 

#### Position embeddings 
Early position embeddings encoded a sinusoid function that was offset by a phase shift proportional to sequence position 
In practice, easiest is to learn position embedding from scratch. 
![[Pasted image 20240507095018.png]]
![[Pasted image 20240507095028.png]]

### GPT : Generative Pretrained Transformer 
Called a decoder transformer 
**But** actual GPT block mixes design of encoder and decoder from original transformer 
Uses masked multi-headed self-attention (decoder) (can't see future)
No cross attention; only computes a self-attention over its history in each block
![[Pasted image 20240507095256.png]]
It is trained to compute the next word ! 


## Transformer beyond text 
Although they were developed for NLP applications, transformers (and the notion of attention) generalize to other modalities

The simplest way is to consider the pixels as words 
But not the image as a all, but as a set of patches
![[Pasted image 20240507095650.png]]