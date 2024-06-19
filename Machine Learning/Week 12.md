
# Lecture 12 : Dimensionality Reduction

## Unsupervised Learning
Until now we used a lot of supervised data (except for next word prediction)
Not so good when you
	• do not have labeled data
	• want to get an understanding of the data
## Dimension Reduction 
Dimensionality reduction is the task of
	• discovering the data manifold
	• getting a low-dimensional representation of the data
		• sometimes at some loss of information (hopefully noise)
![[Pasted image 20240514092808.png]]
Our **goal** is to find a mapping $y_i = f(x_i)$
How about a **linear one** $y_i = W^Tx_i$
## Principal Component Analysis (PCA)
Pearson (1901), Hotelling (1933)
Given N samples {$x_i$}, PCA yields a projection of the form$$y_i = W^T(x_i-mean(x))  s.t. W^TW = I_d
$$
The data is unsupervised 

### Objective of PCA
We aim to keep most of the “important” signal
Yet, we want to remove the noise
This can be achieved by finding directions that have a large variance, i.e., for the jth output dimension, we want to **maximize**.
$$\text{Var}(\{y^{(j)}_i\}) =
\begin{aligned}
&\frac{1}{N} \sum_{i=1}^{N} \left(y^{(j)}_i - \overline{y}^{(j)}\right)^2 \\[1ex]
\end{aligned}
$$

### Variance maximization 
**Lots** of computation, there is eigenvector and lagrangian

=> at the end 
$$w_{(1)}^T C w^{(1)} = \lambda_1 w_{(1)}^T w^{(1)} = \lambda_1$$
The resulting term on the lefthand side is the variance of the projected data
Since we seek to maximize it, we should take $w_{(1)}$ as the eigenvector corresponding to the largest eigenvalue $λ_1$
	• Because the equation above shows that the variance is equal to the eigenvalue


### Dealing with more than 1D projections
To obtain an output representation that is more than 1D, i.e.,d>1 , we can perform recursively
	• The second projection vector $w_{(2)}$ corresponds to the eigenvector **C** of with the second largest eigenvalue
	 • The third vector $w_{(3)}$ to the eigenvector with the third largest eigenvalue
The matrix  **W** is obtained by concatenating the resulting vectors, i.e.,$$W =
\begin{bmatrix}
w^{(1)} & w^{(2)} & \cdots & w^{(d)}
\end{bmatrix}
\in \mathbb{R}^{D \times d}
$$
This guarantees to satisfy the constraint $W^TW = I_d$

In the limit, one can use all dimensions, i.e., set d = D
- There is therefore no reduction of dimensionality
-  In 3D, you can think of this as **a rotation** of the data
- This incurs no loss of information
- The d = D dimensions in the new space are uncorrelated 
![[Pasted image 20240514141721.png]]
Another option is to keep all the eigenvectors that correspond to non-zero eigenvalues
- This means that the data is **truly low-dimensional**
- E.g., this happens when we have fewer samples than dimensions (N < D )
- The resulting {$y_i$} are lower dimensional ( d <D)
- But there is **no loss of information**
![[Pasted image 20240514141925.png]]
In practice, one typically truncates the eigenvalues so as to discard some that are non-zero
- This can be achieved by aiming to retain a pre-defined percentage of the data variance, measured as the sum of eigenvalues
- E.g., to retain at least 90% of the variance, one can search for d such that 
$$\sum_{j=1}^{d} \lambda_j \geq 0.9 \cdot \sum_{k=1}^{D} \lambda_k
$$
- assuming the eigenvalues are sorted in decreasing order
- The resulting {$y_i$} have an even lower dimension

### Error minimization 
This incurs some loss of information
![[Pasted image 20240514142516.png]]
However, the corresponding rectangular matrix **W** is the orthogonal matrix that minimizes the **reconstruction error**(least-square error)$$e_i = \|\hat{x}_i - x_i\|^2
$$Where : $$\hat{x}_i = \overline{x} + Wy_i = \overline{x} + WW^T(x_i - \overline{x})
$$
### Mapping 
PCA not only reduces the dimensionality of the original data. It provides **a continuous mapping** from the low-dimensional space to the high-dimensional one !

That is, for any $y ∈ ℝ^d$ , we can compute a point in the highdimensional space as $$\hat x = \overline{x} + Wy $$This mapping constrains **\hat{x}** to lie in a subspace, and thus provides a form of regularization
![[Pasted image 20240514143134.png]]

## Kernel extension of PCA
### Dealing with nonlinear data 
PCA is a linear dimensionality reduction method
	It just gives projections of the data
How can we achieve nonlinear dimensionality reduction
	E.g., a projection of the Swiss roll would squash it instead of unrolling it 
![[Pasted image 20240514143332.png]]

### Kernel PCA 
Same intuition as in supervised kernel methods:
	Map the data to a high-dimensional space$$x_i \rightarrow \phi(x_i)$$
	Then perform PCA
Our goal will again be to replace dot products with kernel values$$k(x_i,x_j) = \phi(x_i)^T\phi(x_j)$$
***
...
a lot of computations 
...
***
As in kernel ridge regression, this can be addressed by noting that, ultimately, we are not interested in w(1) , but rather in obtaining the low-dimensional projections {$y_i$}

- For a 1D representation, these projections can be computed as$$y_i = \phi(x_i)^T w^{(1)}
= \frac{1}{N} \sum_{j=1}^{N} a_j \phi(x_i)^T \phi(x_j)
= \frac{1}{N} \sum_{j=1}^{N} a_j k(x_i, x_j)
$$
As in the linear case, multiple output dimensions can be obtained by taking the d eigenvectors with largest eigenvalues

### Kernel PCA : Non-centered data
So far, we have assumed that the data was centered in feature space
There is, however, no reason for this to be satisfied
	Even if the original data is centered, the data after mapping to feature space might not be
To handle the more realistic scenario where it isn’t, let us define :$$\widetilde{\phi}(x_i) = \phi(x_i) - \frac{1}{N} \sum_{j=1}^{N} \phi(x_j)
$$
The $(i,j)$ elements of the corresponding kernel matrix is then given by : $$\widetilde{K}_{i,j} = \widetilde{\phi}(x_i)^T \widetilde{\phi}(x_j)
$$
This kernel matrix can in fact be computed based on the original one as :$$\widetilde{K} = K -1_NK - K1_N +1_NK1_N$$Where $1_N$ is an *N x N* matrix with every element equal to 1/N



## Kernel PCA vs PCA
However, in contrast to PCA, Kernel PCA does not provides a mapping from the low-dimensional space to the highdimensional one. 
	We cannot simply compute the high-dimensional version of any point in the low-dimensional space

Can we nonetheless have this inverse mapping with a nonlinear method?
	Solution: Let’s rely on deep networks
## Autoencoders
There is no reason to limit ourselves to linear mappings
Following neural networks, the simplest extension consists of using a nonlinear activation function in the middle
	This is referred to as an *autoencoder*
![[Pasted image 20240514150903.png]]

### General form
In general, an autoencoder can be represented by **two mappings**:
- The encoder going from the input to a latent representation 
- The decoder going from the latent representation to the output
![[Pasted image 20240514151125.png]]
As in the PCA case, the output of the decoder is meant to be similar to the original input

### Deep form
There is no reason to restrict the encoder and decoder to consist of a single layer
We can then create deep *autoencoders* by stacking **multiple** layers, each with an activation function
![[Pasted image 20240514153139.png]]

### Autoencoder training 
With PCA the reconstructed input ${\hat{x_i}}$ minimize the least-square error => $e_i = ||\hat{x_i} - x_i||^2$ 

Following this intuition, an autoencoder can be trained by minimizing the empirical risk 
$$R(\{W^{(j)}\}) = \frac{1}{N} \sum_{i=1}^{N} \|\hat{x}_i(\{W^{(j)}\}) - x_i\|^2
$$
As for MLPs, this is non-convex and minimization is done via stochastic gradient descent (***yes...***)

### Denoising autoencoders
Having a low-dimensional latent representation encourages the autoencoder to learn an “intelligent” mapping

With dimensionality expansion, one could simply learn to copy the input

To avoid this, one can add noise to the input and aim to reconstruct a noise-free version of the input
![[Pasted image 20240514154113.png]]

### Sparse autoencoder
Another approach to learn a meaningful latent representation consists of adding a regularizer
Let $y_i$ denote the latent representation (or code) for sample $i$
Then, one can encourage a large number of the elements $y_i^{(k)}$to go to zero
This can be achieved by a sparsity-inducing regularizer, such as the $L_1$ norm$$E_r(\{W^{(j)}\}) = \sum_{i=1}^{N} \sum_{k=1}^{M} |y^{(k)}_i|$$
### Convolutional autoencoder
With convolutions and upsampling-convolutions, or transposed convolutions, one can also design convolutional autoencoders. 
	• Note that fully-connected layers can also be used in the middle
	• Example from Guo et al., ICONIP 2017	
![[Pasted image 20240514154615.png]]

### Autoencoder : Mapping 
As in PCA, the decoder provides a mapping from the latent space to the output (same as input) space
One can thus generate **new** samples by taking **arbitrary points** in the latent space![[Pasted image 20240514154800.png]]
