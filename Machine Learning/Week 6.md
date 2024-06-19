# Lecture 6 : Overfitting, Cross-validation and Regularization

## Model complexity
As we have moved from linear models to nonlinear ones, we have obtained increasing flexibility 
- We do no longer assume that the output linearly depends on the input
- We can handle classification problems in which the classes are non-linearly separable

This flexibility, however, may sometimes make the model too complex for the task at hand. 

**Example**: 
k-NN choice of k: 
![[Pasted image 20240329143450.png]]
![[Pasted image 20240329143505.png]]
For k = 1, the error is 0 on the training data, but large on the test data. This is called **overfitting** 

For large k values, the error is large on both the training and test data. This is called **underfitting** 

This behavior is **not** specific to k-NN. It can happen in most ML algorithms. 

**Another example** 
Polynomial curve fitting 
![[Pasted image 20240329144031.png]]
While the training curve always decreases as the degree increases, the test curve eventually increases again. 

## Overfitting vs Underfitting 
The general phenomenon can be "resumed" in this graph: 
![[Pasted image 20240329144222.png]]
The main question is to find the *min* in the test curve. For this we will use **Cross-validation** 

## Cross-validation 

### The validation set method
If given a set of data, we randomly choose data to be our **validation set**, for exemple 30% of the data. The remaining data points will be our **training set** 

![[Pasted image 20240329144850.png]]
AND then we can perform regression on this new training set only & estimate the performance of our models thanks to the validation set. 

![[Pasted image 20240329145803.png]]
![[Pasted image 20240329145818.png]]
![[Pasted image 20240329145832.png]]
Here the quadratic function fit best :) 

**Advantages** 
- Very simple 
- We can choose the model based on the validation error

### Leave-one-out cross-validation (LOOCV)
For i = 1,...,N
1. Let $(x_i, y_i)$ be the $i^{th}$ sample
2. Temporarily remove $(x_i, y_i)$ from the training set 
3. Performing regression on the remaining N-1 samples
4. Compute the error on $(x_i, y_i)$

When you have done all point, report the **average error** 

**Example** with the quadratic function: 
![[Pasted image 20240329151629.png]]

### Validation set VS LOOCV
![[Pasted image 20240329151722.png]]


### k-fold cross-validation  
THE BEST OF THE TWO WORLD askip (YES)
Randomly split the dataset into partitions (e.g., k = 3 , shown with different colors).
For the red partition, train on all the points not in the red partition. Compute the errors on the red points.
etc etc....
Compute the average error and we get : 
![[Pasted image 20240329152506.png]]
New super tableau : 
![[Pasted image 20240329152551.png]]

### Summary of cross-validation 
Cross-validation can help to
- Select the best model among a small number of candidates
- Find the best value for one hyper-parameter (e.g., k in kNN)

However, it becomes impractical with too many candidates
- For example, one cannot choose which combination of functions to use for feature expansion among a very large set. 

Let us now look at another way to prevent overfitting by penalizing model complexity via regularization (cette phrase ne veut RIEN dire mais bref)
- To this end, we will go back to the linear model (with expanded features)

## Overfitting with a linear model 
Because the linear model is simple, overfitting occurs fairly rarely. 

Nevertheless, if there are fewer training samples than input dimensions (N <= D + 1), then one can always fit a **hyperplane** that passes exactly through the training samples. 

Example: N = 3, D = 2  
3 noisy samples (red circles) originating from the true blue plane. They are not on the plane because of noise. 
![[Pasted image 20240329153746.png]]
The standard way to penalize the complexity of a model is to add a **regularizer** to the empirical risk. 

This leads to a new training objective function of the form: 
![[Pasted image 20240329153846.png]]
One of the most common **regularizers** is the sum of squares of the weights (there is a probabilistic motivation behind it).
![[Pasted image 20240329154049.png]]
This lets us write regularized linear regression as
![[Pasted image 20240329154117.png]]
This remains a convex function (for λ >= 0) and so we can still find the solution by zeroing out the gradient. 

Grouping the inputs in a matrix Φ and the outputs in a vector , as before, gives the final solution: 
![[Pasted image 20240329154345.png]]

### Multi-output ridge regression
When dealing with multiple output dimensions, the parameters are expressed in matrix form

We can then write multi-output ridge regression as
![[Pasted image 20240329154523.png]]
Where the ||W||^2 is the square Frobenius norm, equivalent to the sum of the square of all values in the matrix W

By zeroing out the gradient, we obtain the solution
![[Pasted image 20240329170104.png]]

## Regularization 

FAIRE UN RESUME ICI (parce que je comprends pas très bien ce que c'est)

Regularization may also be added to the cross-entropy loss used to train the logistic regression model
- With a convex regularizer, such as the sum of squares, the overall regularized risk remains convex
- The scikit-learn implementation of logistic regression has such an L^2 regularizer by default