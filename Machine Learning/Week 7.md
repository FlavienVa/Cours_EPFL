
# Lecture 7 : Kernel Methods

## Introduce the notion of kernel function and kernel trick

The solution to many ML algorithms ends up relying on a dot product between inputs, i.e., $$X_i^TX_j.$$
When doing feature expansion, we then have dot products of the form $$ϕ(X_i)^T ϕ(X_j)$$ which also encodes a similarity between the two samples, but different from the original one. 
Thus, instead of explicitly defining the mapping ϕ(), one can define a similarity function $k(X_i,X_j)$

This is called a *kernel function*. It correspond to some mapping ϕ(), but this ϕ() may be unknown. 

<u>Example </u>
One example of commonly-used kernel function is the **polynomial kernel** $$k(X_i, X_j) = (X_i^TX_j + c)^d$$ 
It has two *hyper-parameters*
	The bias *c*, often set to 1
	The degree of the polynomial *d*, often set to 2

For such kernels, the corresponding mappings ϕ() are known:
	![[Pasted image 20240426172204.png]]

Possibly the most common kernel is the **Gaussian kernel** (or Radial Basis Function (RBF)) $$k(X_i, X_j) = \exp\left(-\frac{||x_i-x_j||^2}{2\sigma^2}\right)$$
It has only one hyper-parameter: 
	The *bandwidth* σ

### Kernel trick
Since a kernel function corresponds to a similarity, as a dot product, one can replace any dot product between two samples in the solution of an ML algorithm with a kernel function. This is applicable to many algorithms, and is referred to as *kernelizing* an algorithm. 


### Kernelization: Notation 
In what follows, with a small abuse of notation, I will also use the kernel function to encode the similarity between one sample and all the training samples, represented via the matrix X. This will give: $$k(X, x_i) = \begin{bmatrix} k(x_1, x_i) \\ k(x_2, x_i) \\ \vdots \\ k(x_N, x_i) \end{bmatrix} \in \mathbb{R}^N$$
Furthermore, I will also use the notion of kernel matrix, in which each entry (i,j) contains the kernel function evaluated between training sample i and training sample j . This matrix is squared and can be written as: $$K = \begin{bmatrix} k(x_1, x_1) & k(x_1, x_2) & \cdots & k(x_1, x_N) \\ k(x_2, x_1) & k(x_2, x_2) & \cdots & k(x_2, x_N) \\ \vdots & \vdots & \ddots & \vdots \\ k(x_N, x_1) & k(x_N, x_2) & \cdots & k(x_N, x_N) \end{bmatrix} \in \mathbb{R}^{N \times N}$$As seen before, the solution to linear regression is given by: $$w^* = (\Phi^T\Phi)^{-1}\Phi^Ty\quad \quad \Phi = \begin{bmatrix} \phi(x_1)^T \\ \phi(x_2)^T \\ \vdots \\ \phi(x_N)^T \end{bmatrix} \in \mathbb{R}^{N \times F}$$
At first sight, it may seem that $Φ^TΦ$ contains the dot products that we want to replace with a kernel function. 
	However,this is an matrix **F x F** encoding dot products between the different feature dimensions, not the different samples
	A kernel matrix, containing the dot products between every pair of training samples would be of size **N x N**, and correspond to (#problem)$$ΦΦ^T\quad not \;\;Φ^TΦ $$
## Kernelized versions of linear regression

#### Representer theorem
To kernelize linear regression we can make use of the **Representer theorem** (this will also be usefull to kernelize other algo)

In essence, this theorem states that the minimizer of a regularized empirical risk function can be represented as a linear combination of the points in the high dimensional space. That is, we can write: $$w^* = \begin{aligned} &\frac{1}{N} \sum_{i=1}^{N} \alpha_i^* \phi(x_i)  =\Phi^T \alpha^* \end{aligned}$$
### Kernel regression 
So instead of searching for a minimizer of the empirical risk in terms of x, we search for one in terms of new variables {αi}. 
![[Pasted image 20240426180820.png]]
The gradient of the risk w.r.t. α is given by: $$\nabla R(\alpha) = \begin{aligned} &\frac{2}{N} \sum_{i=1}^{N} k(X, x_i) \left(k(X, x_i)^T \alpha - y_i\right) \\[1ex] \end{aligned}$$
Following the same reasoning as for linear regression, we can write this in matrix form as: $$\nabla_\alpha R(\alpha) = \begin{aligned} &2KK\alpha - 2Ky \\[1ex] \end{aligned}$$
**K** is the N x N training kernel matrix, obtained by evaluating the kernel function between all pairs of training samples. 

For r a new sample x, the prediction is given by: $$\hat{y} =  \begin{aligned} (w^*)^T \phi(x)   &= y^T (K^{-1} \Phi) \phi(x) \\[1ex] &= y^T (K^{-1} k(X, x)) \end{aligned}$$
Where k(X,x) is the N-dimensional vector obtained by evaluating the kernel function between the training samples and the test one. 

Note that the quantity $y^T(K^{-1})$ only depends on the training data, and thus can be pre-computed (i.e., does not need to be re-computed for every test sample) :)

### Kernel ridge regression 
That is, with the Representer theorem, the regularized empirical risk : 
![[Pasted image 20240426184938.png]]
Following the same reasoning as without regularizer, we can write the gradient of the risk in matrix form as : $$\nabla_\alpha E(\alpha) = \begin{aligned} &2K (K + \lambda I_N) \alpha - 2Ky \\[1ex] \end{aligned}$$Setting it to 0 yields the solution : $$\alpha^* = \begin{aligned} &(K + \lambda I_N)^{-1} y \\[1ex] \end{aligned}$$Given α*, one can then express w* mathematically as in the case without regularization, and eventually use this formulation to make predictions for the test samples. 


### Kernel ridge regression with multiple outputs
To handle nonlinear problems, we can then again work in a different feature space ϕ() and write the regularized empirical risk : $$E(W) = \begin{aligned} &\frac{1}{N} \sum_{i=1}^{N} \left\|W^T\phi(x_i) - y_i\right\|^2 + \lambda\|W\|_F^2 \\[1ex] \end{aligned}$$By making use of the Representer theorem, we can again derive a solution that does not explicitly depend on ϕ( ⋅ ) but rather on kernel values  k(xi , xj ). 
Ultimately, the solution is given by: $$W^* = \begin{aligned} &\Phi^T (K + \lambda I)^{-1} Y \\[1ex] \end{aligned}$$
Where Φ is the same matrix concatenating the training inputs {$ϕ(x_i)$} as in the 1D-output case, and Y € $R^{NxC}$ is the matrix stacking the training output vectors. 

The prediction vector for a test sample is then given as: $$\hat{y} = \begin{aligned} &(W^*)^T \phi(x) \\[1ex] \end{aligned}$$
$$  \begin{aligned}   &= Y^T (K + \lambda I)^{-1} \Phi \phi(x) \\[1ex] &= Y^T (K + \lambda I)^{-1} k(X, x) \end{aligned} $$

## Kernelization 
The idea of kernelizing an algorithm does not apply only to linear regression 
	- It can also be used to obtain a nonlinear version of SVM (via the so-called dual formulation of SVM) 
	- In a few weeks, we will also do this for dimensionality reduction
	- Note that logistic regression can also be kernelized, although this is less commonly done


#  FALLOIR REGARDER DES VIDEOS C UN PEU FLOU