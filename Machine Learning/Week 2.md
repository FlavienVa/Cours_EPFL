	
## 1D linear regression 

With 1D inputs, training a linear regressor aims to minimize: 
![[Pasted image 20240227082908.png]]
Equivalently, we can write :
![[Pasted image 20240227082948.png]]
But in general there is more then 1 input value !!

## Plane & Hyperplane 
When there are two input dimensions instead of a line, we can define a plane. 

This is really easily generalized to D dimensions : 
![[Pasted image 20240227083332.png]]
What ever the dimension, we can write :
$$
y = w^Tx
$$
But this doesn't change the optimisation we have to do, because the output remains 1D, we can use the same least.square loss function as before, and write training as : 
![[Pasted image 20240227083618.png]]

------------------------------------------------------------------------------------------------------------------ 
## Interlude : Derivatives and Gradients
je vais rien noter 
cf anal I/II

-------------------------------------------------------------------------------------------------------------------
## Back to real business
So we seek to minimize the function just up (the min 1/N sum...) that we can call R(w)
This is a convex function of w, which means that we search for parameters  w^3 such that :  (IT IS THE GREAT GRADIENT)
![[Pasted image 20240227084629.png]]
<u>To compute</u> 
3 Hints : 
- Hint  1 : R(w) is an average over the N training samples 
	- As the gradient of an average is the average of the gradients, you can focus on computing the gradient 
	- ![[Pasted image 20240227085214.png]]
 - Hint 2 : Use the chain rule 
	 - The upper function has the form f(g(w)), with f(g) = g^2
	- You can compute its gradient as :
	- ![[Pasted image 20240227085341.png]]
- Hint 3 : To compute the gradient of g(w), you can make use of the following general vector derivative rule: 
	- Given 2 vectors (a,b) in R^D
	- ![[Pasted image 20240227085529.png]]
- For help with matrix computation see --> http://www2.imm.dtu.dk/pubdb/edoc/imm3274.pdf

<u>THE solution</u>
If we put all this mega super hint back to back we get : 
(and grouping all x_i and all y_i in a matrix and a vector like this:)
![[Pasted image 20240227085848.png]]

THE solution : $$ X^TXw^* = X^Ty$$**Or re-write to isolate w :**$$ w^* = (X^TX)^{-1}X^Ty = X^†y$$
† is the dagger (Moore-Penrose pseudo-inverse of X)

 ---------------------------------------------------------------------------------------------------- 
### Exemple : 
![[Pasted image 20240227092315.png]]

--------------------------------------------------------------------------------------------- 
## How well does our model perform?

**Recall** : Training data =! Test data  

<u>Evaluation metrics</u> 
- Mean squared error (**MSE**)
	- ![[Pasted image 20240227092921.png]]

- Root mean squared error (**RMSE**)
	- Square-root of the MSE

- Mean absolute error (**MAE**)
	- ![[Pasted image 20240227093028.png]]

- Mean absolute Percentage error (**MAPE**)
	- ![[Pasted image 20240227093100.png]]

## Interpreting a linear model 

Warning: The magnitude of a *coefficient* will depend on the magnitude of the corresponding feature/attribute

So for that we <u>normalize</u> the data (more on that later) :)

## Dealing with multiple <u>output</u> dimensions 

We don't want to only predict **one** data. 
For exemple to get the human pose estimation, we aim to predict the 3D position of each joint !

To output multiple values, the linear model cannot just rely on a vector w, because the product w^Tx_i yields a single value.
Therefore we use a matrix W € R^(D+1) x C such that : 
![[Pasted image 20240227094331.png]]
Where each w_j is a (D+1) dimensional vector, used to predict one output dimension. 

<u>Training</u> 
We need to slightly modify the loss function : 
![[Pasted image 20240227094558.png]]
To compute the gradient of this function, we use 2 super properties : 1. Norm and derivative / 2. Matrix derivative 
Thus, using the chain rule we obtain the gradient : 
![[Pasted image 20240227094806.png]]
Setting it to zero means that : 
![[Pasted image 20240227094839.png]]

Using the same strategy as before : $$ W^* = (X^TX)^(-1)X^tY = X^†Y$$
## Linear classification 
The goal : Predict one discrete label for a given sample

### Least-square binary classification 
To fit the linear model to training data, the same strategy as for linear regression can be used : 
- Given N pairs {(x_i, y_i)}, we can use the least-square loss to write : 
- ![[Pasted image 20240227095641.png]]
It has exactly the same formulation as before, so the solution is exactly the same : $$ w^* = (X^TX)^(-1)X^Tx $$
The **problem** : it output a continuous value 

The **solution** : thresholding - ->
![[Pasted image 20240227095920.png]]

