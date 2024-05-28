# Multilayer perceptron (MLP) model

The representations and data processing strategies we have discussed so far were all **manually designed**. There is, however, no guarantee that they are the best representations for the task at hand. Ideally, one would therefore like to learn the representation jointly with the classifier.
--> Artificial neural networks are a way to do so


### Motivating exemple : Curve fitting
In the 1D input and 1D out scenario, we would like to find a mapping from x to y that approximates the curve below: 
![[Pasted image 20240423083133.png]]
We want to learn the feature expansion and the linear model jointly. 

To achieve this, we can define a parametric feature expansion strategy. 
Because we like linear models, let’s use one for this purpose. Naively, we could then write the mapping :
![[Pasted image 20240423083431.png]]
But this doesn't work... We need to add nonlinearity in the process. 

We are going to use a nonlinear fonction *f*, for this we define : 
![[Pasted image 20240423083606.png]]
Then, our parametric feature expansion process can be expressed as :
![[Pasted image 20240423083636.png]]
where the final notation applies the function f() in an elementwise manner to the M-dimensional vector output by the linear mapping. 
In the context of artificial neural networks the function f() is referred to as the **activation function**. 

<u>Examples of activation function</u>
![[Pasted image 20240423084002.png]]
<u>Hidden representation</u>
The M-dimensional vector :
![[Pasted image 20240423084129.png]]
is commonly referred to as hidden representation (or hidden layer). Each one of its M elements is referred to as a hidden unit.


With this we get -->
<u>The Complete model</u>
We can then perform prediction by applying a second linear model to the hidden representation.
-->
![[Pasted image 20240423084235.png]]
If we go back to our example : 
We can approximate any continuous function with a linear combination of translated/scaled ReLU functions f( ⋅ )
![[Pasted image 20240423084643.png]]
This is also true for other activation functions under mild assumptions. 

## Dealing with multiple input dimensions
From the 1D input formulation: 
![[Pasted image 20240423084900.png]]
we simply write the D-dimensional case as: 
![[Pasted image 20240423085037.png]]
To handle C>1 output dimensions, we can proceed as in the case of linear models. We simply need to replace the vector of parameters with a matrix of parameters C > 1 w(2) W(2) ∈ ℝM×C

The prediction can then be written as : 
![[Pasted image 20240423085149.png]]

This resulting in a simple artificial neural network with one hidden layer can be depicted as : 
![[Pasted image 20240423085259.png]]
Here if we look a the first hidden unit we see a line with all the input because it **depends** of all the input (x1...xD)

Because we use a matrix-vector product for each layer, the layers are called *fully-connected* (in this course will we only do fully connected)

## Multilayer Perceptron 
With a single hidden layer, one can already model complicated functions. However, why stop at just one hidden layer. 
![[Pasted image 20240423091723.png]]
!!! Slightly misleading name: 
	The perceptron uses a step function as activation function
	MLPs typically rely on continuous functions 

In essence, each hidden layer takes as input the output of the previous layer. For the first hidden layer, we still have : $$z_{(1)} = f(W^T_{(1)} x)$$
For any subsequent layer l, we have : $$z_{(l)} = f(W^T_{(l)} z_{(l-1)})$$
Finally, for a network of L layers, we compute the output as:  $$ŷ = W^T_{(L)} z_{(L-1)}$$
The process of going from the input to the output is called **the forward pass of the network**
If we wanted to write the prediction as a single equation, it would look like: $$ŷ = W^T_{(L)}f(W^T_{(L-1)}f(W^T_{(L-2)}f(...)))$$ This is not very convenient, and it is easier to simply think of y ̂ as a function of all parameters : $$ŷ = ŷ(W)$$
## Training an MLP
Given a set N of training samples (xi , yi ), we would then like to find the best set of parameters  W*. 
As usual, this is done by minimizing an empirical risk w.r.t. all the parameters **W**
For regression, the loss function is typically the square loss : 
![[Pasted image 20240423092742.png]]
Because an MLP stacks multiple layers, each of which has a nonlinear activation function, the problem does not have a closed-form solution and is not convex. Learning is therefore done by **gradient descent**

### <u>Gradient-based learning</u> 
Because the empirical risk is an average over the training samples, its gradient will also be an average of gradients. 

.... It is very complex and wont be at the exam .... (so I will just skip to the end result)

--> Algo : 
1. Propagate x_i forward through the network
2. Compute δ(L) depending on the loss
3. Propagate δ(L) backward to obtain each δ(l)
4. At each layer, compute the partial derivatives
![[Pasted image 20240423093849.png]]
Once the gradient is computed, the update at iteration k is performed as : $$ W_k <- W_{k-1} - η∇_W R(W_{k-1})$$
where W encompasses the parameters of all layers
You can also think of this as updating the parameters in each layer l as:
![[Pasted image 20240423094201.png]]

To train neural network we mostly use : **Stochastic gradient descent** 

### <u>Stochastic gradient descent (SGD)</u>
Stochastic gradient descent updates the parameters W based on the gradient of a single sample in each iteration. 
![[Pasted image 20240423094435.png]]
In practice, one then iterates over the training samples, either in a fixed order or in a random one. 

The gradient obtained by a single sample might be a poor approximation of the true, complete gradient. 
To obtain a more reliable gradient estimate, one typically uses **mini-batches** of B samples. 
![[Pasted image 20240423094820.png]]

<u>SGD behavior</u> 
Because it approximates the full gradient, SGD will move the parameters less directly towards a local minimum. 
	The parameters dance around the minimum, which may require decreasing the learning rate
	Nevertheless, SGD can help to avoid bad local minima
	![[Pasted image 20240423094930.png]]

<u>Limitations of gradient descent</u>
Gradient descent makes strong assumption about the magnitude and isotropy of the local curvature so that the same step size can be used in all directions. 
![[Pasted image 20240423095144.png]]
![[Pasted image 20240423095156.png]]
We can add **SGD extensions** to change that. 
for example : 
**Momentum** 
	Add inertia to the parameter updates
	![[Pasted image 20240423095259.png]]
	![[Pasted image 20240423095314.png]]

There is a lot of SGD extension (Adagrad, ADAM,...) but very ***hard*** 


## From regression to classification 
So far, we have focused on the case where the prediction is taken directly as the output of a linear model applied to the last hidden representation, i.e., $$ŷ =  W^T_{(L)}z_{(L-1)}$$
While this is fine for a regression task, it is not ideal for a classification one, where we would like each  $y ̂ ^{(k)}$ to represent **the probability (score)** for class k. This can be achieved via the *softmax function* (as in multi-class logistic regression)
![[Pasted image 20240423100105.png]]
In this case, the empirical risk is typically defined as the cross-entropy: 
![[Pasted image 20240423100121.png]]
Backpropagation then requires computing the derivative of the cross-entropy w.r.t. each y^(k) and the derivative of the softmax function w.r.t. each W^(j,k)_(L)_
	Note that this is similar to the derivatives computed for multi-class logistic regression