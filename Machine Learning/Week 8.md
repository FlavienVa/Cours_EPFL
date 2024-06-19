
# Lecture 8 :Data representations and one general method to represent data
## Data can be
- Attributes 
- Text
- Image
- Sound 
- and so on...

## The question is : How can we represent this data??

### But first : Why do we need data representation ?

<u>Vectors are not efficient...</u>
We can use vectoorrss, but it becomes really big really fast!
![[Pasted image 20240416084338.png]]
And also we can not compare the vector if they have different dimension....

<u>Data noisiness</u>
- Not every piece of data encodes valuable information
- Example :
![[Pasted image 20240416084831.png]]

### Our solution : Attributes

<u>...To dictionaries</u>
We have *n* sample of text
Idea: For each sample, count the number of times each word in the dictionary appears.

But we are not going to take the english dictionary, it is to big and not useful. Instead we are going to take all the world from our samples and make it a dictionary. 

This is very useful because we can make vector of nbr of appearance of our word in the new created dictionary.
![[Pasted image 20240416085540.png]]

For text samples of different length, the actual word counts may not be relevant
	Longer texts will just have larger values (not good)

Instead, one can encode the proportion of each word, by dividing each vector by the sum all elements. 
![[Pasted image 20240416085832.png]]

<u>...How about images?</u>
There is not "words" in a image, BUT there are commun **parts/patches** 
example mignon: 
![[Pasted image 20240416085948.png]]
that leads us to -->
<u>Visual dictionary</u>
Extract patches from all images
![[Pasted image 20240416092354.png]]
Each patch is unique, so it cannot be considered as a word
	However, some patches look similar

Group the patches based on similarity to obtain prototypes that act as dictionary words
![[Pasted image 20240416092445.png]]
The center of each cluster (group) is taken as a visual word

For each patch in a image: 
- find the nearest visual word 
- add one to the corresponding element in a BoW vector
![[Pasted image 20240416092554.png]]


<u>To summarize:</u> 
Large, heterogeneous data can be made compact and homogenous
The resulting representations can be directly used as input to an ML algorithm
- This is applicable to all the methods we have seen so far
- But in general to most machine learning algorithms
# Data processing techniques

## But first PRE-processing
Even with a good representation, the data might benefit from being pre-processed
This is because, e.g.
- Some attributes values are missing 
- Some attributes values are noisy 
- The feature dimensions are of incommensurate magnitudes

<u>Missing data</u>
We can have some data that were not recorded, usually we know it!

Potential solutions : 
- Ignore the corresponding samples
- Fill the missing values with a global constant
- Fill the missing values with the corresponding attributes average over the dataset
- Fill the missing values with the corresponding attributes average over the samples belonging to the same class 

<u>Noisy data</u>
Noise: 
	Random measurement error

Incorrect values
	Faulty data collection
	Data entry problems
	Data transmission problems

Other problems 
	Duplicates entry
	Inconsistent data

<u>How can we clean noisy data?</u>
1.Binning
	For each attributes 
	1. sort the data
	2. Partition in into bins
	3. Smooth the data by taking the bin means, or medians
	![[Pasted image 20240416093722.png]]

2.Clustering 
	Detect outliers as the clusters with too few elements and remove these samples


<u>Data Normalization</u>
Goal: Scale each individual attribute/feature dimension to fall within a **specified range**

- Min-max normalization
	For each feature dimension d, compute the normalized feature
	![[Pasted image 20240416094115.png]]
	![[Pasted image 20240416094121.png]]

- z-score normalization
	For each feature dimension d, compute the normalized feature
	![[Pasted image 20240416094158.png]]
	![[Pasted image 20240416094207.png]]

- Max normalization 
	For each feature dimension d, compute the normalized feature
	![[Pasted image 20240416094751.png]]
	![[Pasted image 20240416094757.png]]

- Normalization by decimal scaling
	For each feature dimension d, compute the normalized feature
	![[Pasted image 20240416094840.png]]

***!!!*** Normalization of the data is not always necessary, it may or may not help depending on the data at hand and the ML algo

# Class imbalance and solution to address it
**Example of imbalanced data:** 
You train a classifier to predict is a subject suffers from cancer (positive) or not (negative) from image data
On validation data, your classifier gives you 99.1% accuracy (YEAH)
Out of curiosity (or thoroughness), you still look at the confusion matrix: 
	![[Pasted image 20240416095220.png]]

WHAT HAS HAPPEND ??!?
![[Pasted image 20240416095246.png]]
Most of the samples are negative.. 
	So the classifier just learned to predict "negative" most of the time


**Other examples :** 
Between-class imbalance
![[Pasted image 20240416095358.png]]
Within-class imbalance
![[Pasted image 20240416095408.png]]

We have to main approches to act on this imbalanced data : 

### Sampling Methods 
In this method we act on the **data**

We can compensate the lack of data by : 
1. Decrease dataset
	Remove redundant data points form the largest class 
	.
	Naive (random) undersampling: 
		Randomly remove samples from the largest class until until you obtain a balanced data set 
		Drawback: May remove important samples 
	More clever undersampling : 
		Remove the redundant samples 
		Good if the class to undersamples is densely populated
		![[Pasted image 20240416102511.png]]


2. Increase dataset
	Generate new data points for the smallest class
	Naive (random) oversampling 
		Replicate the data samples from the smallest class until obtain a balanced data set 
		Drawback : May lead to overfitting
	More clever oversampling :
		Create new data samples based on neighboring existing ones 
		This may create noisy samples
		![[Pasted image 20240416102735.png]]

### Cost-sensitive Methods 
<u>Empirical risk</u> 
Given *N* training samples {$(x_i, y_i)$}, the empirical risk is defined as : 
![[Pasted image 20240416103203.png]]
In general, each loss term $l(y_i, y_i)$ **has the same influence**
With imbalanced data, this means that most of the empirical risk comes from the largest class. 

So a simple solution to this problem is to re-weighting the samples, to put **more emphasis** on *the rare class* 
This give us : 
![[Pasted image 20240416103459.png]]
β can be computed as the inverse proportional to the class frequency: $$ β_i = 1 - N_k/N$$
This strategy can be used in many cases : 

- Least square classification : 
	![[Pasted image 20240416103714.png]]
- logistic regression :
	![[Pasted image 20240416103944.png]]
- Support vector machine (SVM)
	![[Pasted image 20240416104022.png]]

In the three cases, since the weights are fixed during optimization, the respective optimization problems remain convex and can thus be solved to optimality