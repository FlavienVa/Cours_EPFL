
# Lecture 5 : k-Nearest Neighbors & Feature Expansion
 
## Nonlinear Algorithm : the nearest neighbor
This algo is very simple and consist only of : 
1. Compute the distance between the test sample *x* and all training samples ${x_i}$
2. Find the sample $x_{NN}$ with minimum distance 
3. Assign the corresponding label/value $y_{NN}$ to the test sample

The result may change for different **distance function** (logic)

---------------------------
### Interlude - Histograms
A histogram is a D-dimensional vector such that : 
![[Pasted image 20240326111230.png]]

Given a vector $R^D$ in whose values are all non-negative (and at least one is strictly positive), one can obtain a histogram by dividing each value by the sum of values. 

Exemple : 
x = [1 2 1 2 1 1 0 0 0]^T
--> the histogram is : x = [1/8 1/4 1/8 1/4 1/8 1/8 0 0 0]^T because the sum is 8

***

<u>Different distance function</u>

- Euclidean distance : 
- ![[Pasted image 20240326111605.png]]
- Chi-square distance : (often used for histograms)
- ![[Pasted image 20240326111641.png]]

PROBLEM : The results may be sensitive to outliers 

SOLUTION : 
## k-Nearest Neighbors 
"Marche à suivre *(baguette baguette)*": 
1. Compute the distance between the test sample x and all training samples ${x_i}$
2. Find the *k* samples {$x_{NN1}, ...., x_{NNk}$} with minimum distances 
3. Find the most common label among these *k* nearest neighbors (majority vote)
4. Assign the corresponding label $y_{MV}$ to the test sample
If several labels appear the same number of times among the -NN, one strategy consists of taking the one whose samples have the smallest average distance to the test sample. 

<u>Regression</u>
1. Compute the distance between the test sample x and all training samples ${x_i}$
2. Find the *k* samples  {$x_{NN1}, ...., x_{NNk}$}  with minimum distances
3. Compute the value ŷ for the test sample based on that of these k-NN

**Two strategies** 
-  Use the average of the k-NN values {y_NNi}
![[Pasted image 20240329141358.png]]

- Use a weighted average of the k-NN values, based on the inverse distances
![[Pasted image 20240329141445.png]]

<u>Properties</u> 
No learning per se! What we need is : 
- A good data representation
- A distance function
- A given value *k*

This is referred to as a <u>non-parametric</u> learning method 
- No parameters to optimize during training 
- *k* is said to be *hyper-parameter*, set manually (or by **validation** (see future lecture))

Nevertheless, *k*-NN can give **very good results** 

BUT it is computationally expensive, for each test sample, one needs to compute the distance to ALL training samples

--> SOLUTION : Approximate nearest neighbor search (Hashing, kd-trees)

<u>Curse of dimensionality</u>
In the real world, data representation tend to be high dimensional, 
	For example: vectorizing an image yield to a huge vector

SO computing the distance between point is heavy and all points tend to be far apart. 
k-NN is as a result very inefficient. 

FORTUNATELY, real data often lies in much smaller sub-spaces, we will talk later how to find the low-dimensional sub-space of a given dataset. 

<u>Summary of k-NN</u> 
**Advantages**: 
-  Simple method
- Effective to handle nonlinear data
- Only requires defining one parameter (k)

**Drawbacks**:
- The results depend on *k*
- Becomes expensive as N and/or D grow
- May be unreliable with large D


## Non-linear classification 

### Polynomial curve fitting 
We seek to find a polynomial function that approximates the true curve. 
Such polynomial function of degree *M* can be written as: 
![[Pasted image 20240329134403.png]]

### Polynomial feature expansion :
**EXAMPLES**: 
![[Pasted image 20240329134631.png]]
Then we can apply a linear model to this non-linear data. 
For example:
![[Pasted image 20240329134917.png]]
![[Pasted image 20240329134927.png]]


### Working with expanded feature
We can then use a linear model in this new space and write
![[Pasted image 20240329135541.png]]
For linear regression, we used the least-square loss :
![[Pasted image 20240329135651.png]]
Which is re-written as : 
![[Pasted image 20240329135700.png]]
The gradient: 
![[Pasted image 20240329140136.png]]
 We can group the transformed inputs {$ϕ(x_i)$} and the outputs {$y_i$} in a matrix and vector of the form: 
 ![[Pasted image 20240329140307.png]]
 THEN we have: 
 ![[Pasted image 20240329140320.png]]
<u>Mapping to higher-dimensional space</u>
We want to map our data to higher dimensional space (like in the example upper)

This give us more precision in the model, BUT be careful not to over do it 
![[Pasted image 20240329140820.png]]
(the real curve is in green)

<u>Why limit ourselves to polynomials?</u>
In fact we can use any function we want, sine and cosine, exponential and logarithm.. 
We just need to choose what fit best our data (see in 2 weeks)

