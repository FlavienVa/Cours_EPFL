
# Loss fonction & Empirical Risk

<u>The loss fonction :</u> $$ l(û_i, u_i)$$
	computes an error between the prediction and the true value. 

So far we have seen the following loss functions for linear regression : (but there exist a lot more)
- Single output dimension : $$l(û_i, u_i) = (û_i - u_i)^2$$
- Multiple output dimensions : $$l(û_i, u_i) = ||û_i - u_i||^2$$
<u>Empirical risk :</u> 
The empirical risk is define as $$R({x_i},{u_i},w) = 1/N Σ l(û_i,u_i)$$Given N training samples ${(x_i,y_i)}$

During training, our goal is to find the parameters w (w0 and w1) that minimize empirical risk.

Minimizing the risk :
	This is expressed as the optimization problem :
	
	![[Pasted image 20240305085218.png]]
	We can also write that the best parameters are the solution : 
	![[Pasted image 20240305085249.png]]


# Decision boundary 
In graphs where we have a binary solution (and not continuous), the point where the predicted label change from 0 to 1 forms a *decision boundary* 
![[Pasted image 20240305085446.png]]
In a 1D input it is a point & in a 2D inputs it is a line etc...

# Adding non-linearity 
The output of our known model (linear) is a continuous value 

But sometimes it is not what we seek to achieve, we want a more step-like fonction ->
![[Pasted image 20240305091804.png]]
But this type of function is almost always 0 for the gradient, which is not cool for it to be optimize :(

Instead we use a smooth approximation of the step function -->
![[Pasted image 20240305092150.png]]
## The effect of w 
With a 1D input x, applying the logistic function to the output of a linear model gives a prediction

<u>If we modify w(0)</u>
![[Pasted image 20240305092332.png]]
<u>If we modify w(1)</u>
![[Pasted image 20240305092352.png]]

This helps a lot when we want to fil function that have **outliers** 
EX:
![[Pasted image 20240305092524.png]]
## Whit D dimensions 
In the binary case, with D dimensional inputs, we can write the prediction of the model as : $$ û= σ(w^Tx) = 1/(1+exp(-w^Tx))$$
This can be interpreted as the **probability that x belongs to the positive class** 

It is called <u>logistic regression</u> 

## Logistic regression : Cross-entropy
The logistic regression loss is derived by the first writing the following likelihood (We can think about it as a value representing how well the true labels are predicted given w)
![[Pasted image 20240305093403.png]]
But we take this fonction (cause it's better): 
![[Pasted image 20240305093951.png]]
It is called <u>cross-entropy</u>
Since the true y_i is either 0 or 1, the cross-entropy can be re-written as :
![[Pasted image 20240305094203.png]]
![[Pasted image 20240305094514.png]]
To minimize it via gradient descent, we need to compute its gradient and using the chain rule and after some derivations we have:
 ![[Pasted image 20240305094657.png]]
**FINALLY**, this can be put back together to obtain 
![[Pasted image 20240305094757.png]]

# Linear models 
The real diff between these methods is **the loss function** 
![[Pasted image 20240305095027.png]]
# Margin & Support Vector Machine (SVM)
The distance between the decision boundary and the nearest sample is called *margin*
![[Pasted image 20240305095433.png]]
A good decision boundary should <u>maximize</U> the margin

## SVM
The SVM classifier is a binary classifier based on the linear model as before: $$û=w^Tx$$
It aims to maximize the *margin*

The loss fonction of the SVM is : 
![[Pasted image 20240305095850.png]]
"pipi le caca est sorti du cucu" eric

