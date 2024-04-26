# What is machine learning: 

It is a way to predict a output given a sample, like learning in real life.
For that we need data, a algorithm to process it and then we get results !

In this course we will study 2 types of ML problems 

### Two types of problem : Regression and Classification

Regression : Predict **continuous values** for a given sample 
	Exemple : predict te birth weight of a baby
In regression the value typically follow an **order**

Classification : Predict **one discrete label** for a given sample
	Exemple : Multi class image recognition (cat vs tree vs car; 0 vs 1 vs 2)
This time in classification the categories do **not follow an order**, predicting a cat when the image is a car is as false as predicting a tree

### What classes of algorithms ?

<u>Reinforcement learning :</u> 
	Learn to react to the environment 
	NOT COVERED

<ins>Supervised learning :</ins>
	Relies on supervised data (we give them the information)
	Stage 1 : We give a **lot of data** to train the algorithm to find similarity (like images)
	Stage 2 : **Test** the model to see if he is correct 
	
Assumptions (for this course)
The annotations (supervisory signal) are the insight that we **seek** to obtain from the data
Training and test sample are drawn from the **same** statistical distribution 

**!! Training data must be separate for the testing data !!** 

<u>Unsupervised learning : </u>
	Relies on unsupervised data 
	The goal is rather to analyse the observed data set & group them, **find similarity**, through for exemple the linear model 

<table border ='2'>
	<tr>
		 <td> salut </td>
		</tr>
</table>


# Our first ML model : The Linear Model 

## Notation 

![[Notation.png]]

## Simple exemple
Predict the weight of a fish given its length

We juste have two 1 Dimensional data. So we just put the data in a simple graph.
We approximate it with a **line**
![[Pasted image 20240220095316.png]]
## Linear regression 
 To predict this **line** we use linear regression. 
 This line will be the one who fits **the best** our data, but will not be exactly following the data. 
  
<u>Error</u>
The **error** can be measured by the Euclidean distance of the point from the line :
$$
	d(y_i, y_i) = \sqrt{((y_1-y_1)^2)}
$$

<u>Training</u>
**Training** of this model can be seen as a *least-squares minimization* problem 