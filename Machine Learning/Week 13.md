
# Lecture 13 : Clustering 

## Clustering 
The clustering is the fact of grouping data without performing any data transformation. This is an unsupervised task !
## K-means clustering 
Given a set of input samples, group the samples into K clusters. K is a hyper-parameter, assumed to be known/given. 
![[Pasted image 20240521083519.png]]
#### <u>Intuition</u>
The distances between the points within a cluster should be small
The distances across clusters should be large
![[Pasted image 20240521083608.png]]
This can be encoded via distances to cluster centers {${μ_1,…, μ_K}$}

#### <u>Algorithm</u>
1. Initialize  {${μ_1,…, μ_K}$}, for example *randomly*
2. While not converge 
	1. Assign each point $x_i$ to the nearest center $μ_k$ 
		1. For each point $x_i$ , compute the Euclidean distance to every center {${μ_1,…, μ_K}$}
		2. Find the smallest distance
		3. The point is said to be assigned to the corresponding cluster (note that each point is assigned to a single cluster)
	2. Update each center $μ_k$ based on the points assigned to it 
		1. Recompute each center $μ_k$ as the mean of the points that were assigned to it

The difference in assignments or center locations between two iterations can be used as criteria to stop the algorithm. 

#### <u>Properties</u>

✓ Simple algo guaranteed to converge 
✓ Return exactly K cluster

x Does not always converge to the best solution 
x Some cluster might be empty 
x Requires the user to determine K 

=> Elbow method 
	run the algo with different K
	For each sol, sum the distances of each point to its assigned cluster 
	Plot the resulting curve
![[Pasted image 20240521085018.png]]
	The elbow of the curve is where the drop in within-cluster distances becomes less significant. Open to interpretations 

✓ K-means clustering is unsupervised 
	Without annotations, we then need to label the clusters, with annotation, we use them to label the cluster

✓ The Euclidean distance works well for data with homogeneous dimensions. 

x In practice, this is not always the case 
	Different data dimensions can have completely different magnitudes
	They can encode completely different types of information

=> Use different distance function / normalize data
## Fisher Linear Discriminant Analysis
The dimensionality reduction algorithms we saw last week were unsupervised and thus may not preserve category information. 
Bad example : 
![[Pasted image 20240521085809.png]]
Solution, use the class labels during the dimensionality reduction to encourage a *good* clustering. 

Intuitively, we want:
	The samples from the same class to be clustered
	The different classes to be separated 
![[Pasted image 20240521090420.png]]
Note that we are back to supervised learning
	 However, the supervision in not given for the {${y_i}$} directly but via the class labels

### Fisher LDA
a lot of computations ...

We want to maximize : $$J(w^{(1)}) = \frac{E_B(w^{(1)})}{E_W(w^{(1)})} = \frac{w^{(1)T} S_B w^{(1)}}{w^{(1)T} S_W w^{(1)}} $$
$S_W = \sum_{c=1}^{C} \sum_{i \in c} (x_i - \mu_c)(x_i - \mu_c)^T$ 
$S_B = \sum_{c=1}^{C} N_c (\mu_c - \overline{x})(\mu_c - \overline{x})^T$ 

still a lot of computation ..

$$S_Bw_{(1)} = λ_1S_Ww_{(1)}$$
This implies that $w_{(1)}$ must be the solution to a generalized *eigenvector* problem
Left-multiplying both sides by $w_{(1)}^T$  and dividing by $w_{(1)}^TS_Ww_{(1)}$ tells us that $w_{(1)}$ should be the eigenvector with largest eigenvalue

To project the data to more than a single dimension, we can follow a recursive strategy similar to the PCA one
	Ultimately, this consists of taking the d eigenvectors with largest eigenvalues
## Spectral clustering
We want to compute the **connection** between points to create our clusters

### Spectral clustering : Graph-based connectivity 
Group the points based on edges in a graph
- Strong connections indicate points that should be clustered
- Weaker ones suggest that the graph can be cut into pieces/partitions
![[Pasted image 20240521093753.png]]

<U>How to create the graph?</u>
Compute a notion of similarity between the points
	E.g., the similarity between point i and point j can be taken as
$$W_{ij} = \exp \left( -\frac{\|x_i - x_j\|^2}{\sigma^2} \right)$$
With this we would have a **fully connected** graph. 
	But we can take only the first K neighbor to ease computations 

We can also represent it as a matrix : 
![[Pasted image 20240521094044.png]]

<u>Minimize the cut</u>
![[Pasted image 20240521094637.png]]
A cost for cut is obtained by summing the weight of the edges that connect the two groups: 
$$\text{cut}(A, B) = \sum_{i \in A, j \in B} W_{ij}$$
We want to partition our data such that it minimize the $cut(A,B)$ 


<u>Normalized cut</u>
Minimizing the cut might favor imbalanced partitions
	Like a single point in A and all the other point in B
So we want to use a normalized cut : 
$$Ncut(A,B) = \frac{cut(A,B)}{Vol(A)}+\frac{cut(A,B)}{Vol(B)} $$
Where $Vol(A)$ is the volume of partition A, defined $\text{vol}(A) = \sum_{i \in A} d_i$ 

This problem can then be relaxed as : 
$$\min_{\mathbf{y}} \mathbf{y}^T (D - W) \mathbf{y}$$
where $W ∈ ℝ^{N×N}$ is the similarity matrix between all pairs of points  
$D ∈ ℝ^{N×N}$  is the diagonal degree matrix, with $D_{ii} = d_i $ $y$ indicates to which partition each point belongs.
