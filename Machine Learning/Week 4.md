
# Metrics to evaluate a classifier 

## Confusion matrix
We can use this model in binary case!

![[Pasted image 20240312084509.png]]
TP: True positive (number of samples correctly classified as positive)
FP: False positive (number of samples incorrectly classified as positive)

TN: True negative (number of samples correctly classified as negative)
FN: False negative (number of samples incorrectly classified as negative)

P: Total number of positive samples
N: Total number of negative samples

### Classification metrics

With this model we define <u>accuracy</u> (the percentage of corretly classified samples)$$ Accuracy= (TP+TN)/(P+N)$$
But accuracy is not sufficient to understand if our data is good, for that we add the <u>precision</u> in our model. $$Precision = TP/(TP+FP)$$
Which is the percentage of samples classified as positives that are truly positives. 
BUT we need MORE, that's why we have <u>recall</u>, the percentage of positive samples that are correctly classified as positives $$Recall = TP/P$$
ONE MORE -> <u>false positive rate</u>, the percentage of negatives samples that are incorrectly classified as positives $$FalsePositiveRate = FP/N$$
THE LAST ONE : <u>F1 score</u>, single number that combine precision and recall $$F1 = 2* (Precision*Recall)/(Precision+Recall)$$
## ROC curves 
Many classifiers output a score/confidence for their predictions (e.g., y is a continuous value between 0 and 1 in logistic regression)

The decision between positive and negative can be computed by thresholding this score: 
![[Pasted image 20240312090232.png]]
The **Receiver Operating Characteristic (ROC)** curve plots the true positive rate (recall) as a function of the false positive rate, obtained by varying the score threshold. 
Exemple: 
![[Pasted image 20240312091144.png]]
Exemple: 
![[Pasted image 20240312091519.png]]


# Move from binary to multi-class classification problems
In general data require that the output is not a simple binary output (true/false), but more something like categories or labels (cat, dog, car,...). 

## Multi-class least-square classification 
If we want to use the **least-square** classification, we could give integer values to the label.
	But that would also means that if the car is 1, the dog 2 and the cat 3, if we have a 1 instead of a 2, it would means that the dog is more a car than a cat?!? It make no sense (hope I explain it well)
	• E.g., mistaking a car for a tree would be less strongly penalized than mistaking it for a cat (ce que je voulais expliquer)

The most common approach to modeling a multi-class problem consists of encoding the class label as a **vector** with a single 1 at the index corresponding to the category and 0s elsewhere. For Exemple (a sample 2 in a 5-class problem)
![[Pasted image 20240312092923.png]]
- This is referred to as <u>one-hot encoding</u>
Predicting such a one-hot encoding is then similar to making the model output multiple values, as we saw for **multi-output regression**

We use the same system : 
![[Pasted image 20240312093250.png]]
where each w is a (D+1)-dimensional vector. 

Multi-class classification is then similar to a multi-output regression problem. And we can write training as : 
![[Pasted image 20240312093534.png]]
![[Pasted image 20240312093715.png]]
![[Pasted image 20240312093727.png]]


## Multi-class least-square classification : Drawbacks 
As in the binary case, multi-class least-square classification suffers from the fact that the loss function does not reflect the underlying classification task. There we added a non linearity to the output (la courbe 1/1+exp(f)). 

We of course also need that for **the multi-class case.** 
# Representing multiples classes
As in the least-square classification case, we can use one-hot encoding to represent multi-class labels. Then we need to use a matrix W € R^(D+1) X C. In this case the probability for a class k is given by the *softmax* function : 
![[Pasted image 20240326101539.png]]
The empirical risk can then be derived from the multi-class version of the cross entropy, which yields :
![[Pasted image 20240326101817.png]]
	As in the binary case, a single y_*i*^(k) is 1 for every sample *i*, so we really have one term per training sample
	As in the binary case, training is done by minimizing this empirical risk using <u>gradient descent</u>

<u>Gradient descent: Dealing with a matrix</u>
With multiple classes, the parameters are shaped as a matrix W, not as a vector anymore. 
BUT this does not affect the algorithm: (it just means that the gradient itself will also be a matrix)
![[Pasted image 20240326103747.png]]
So the whole algorithm works with matrix entities
	Convergence check can be done based on the Frobenius norm: 
	![[Pasted image 20240326103848.png]]

By following the chain rule and using the gradient of the *softmax* function, we can derive the gradient of the multiclass cross-entropy as: 
![[Pasted image 20240326104019.png]]
At **test time**, given a new input sample x, the probability/score for any class k is computed via the *softmax* function as: 
![[Pasted image 20240326104352.png]]
The ***final class label*** is then predicted as : 
![[Pasted image 20240326104442.png]]
That is, as in least-square classification, the label is the index (position) of the maximum value in the vector y

## Classification evaluation of Multi-class case

• Confusion matrix: Multi-class case
-  Numerical example for a dataset with 3 classes (UCI Iris dataset)
- Note that the matrix is transposed (predicted labels as columns and actual labels as rows), which can also be used in practice.
![[Pasted image 20240326104832.png]]

<U>Metrics</u> 
Accuracy: 
	A global accuracy can be obtained by summing the correct predictions for all classes (diagonal values of the confusion matrix), and dividing this by the total number of samples (sum of all entries in the confusion matrix)
Precision, recall, FP-rate and F1-score:
- The simplest approach to handling these metrics is to compute them for each individual class, and then average over the classes
- In this case, each individual class is in turn considered as the “positive” class while the others act as the “negative” class
- This does not account for class imbalance (some classes may have many more samples than others; more about this in a future lecture)
- This can be handled by weighting the metric for each class by a value depending on the proportion of samples from that class
