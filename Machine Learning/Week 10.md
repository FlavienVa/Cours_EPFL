
# Lecture 10 : Deep Learning 

## Convolutional Neural Network (CNN)

### Convolutional layer
This is a sort of filter to reduce the size of our data. As in this example:
![[Pasted image 20240430083819.png]]

Note that each convolutional output is passed in an activation function. So, in fact, we have : 
![[Pasted image 20240430084011.png]]
The activation function is applied in an element-wise manner, i.e., to each spatial location **independently** 

We can then use **multiple filters** to create multiple channels : 
![[Pasted image 20240430084131.png]]

### Pooling layer 
We want to have a more **global representation**  (VS the local information of the hidden unit)
For this we do pooling (which is a big reduction of the data), we have to strategies : 
- max pooling
- Average pooling
![[Pasted image 20240430084500.png]]


-> And then : **stack** multiple such blocks
- The number of channels can vary (below: first 3, then 6)
- The size of the filters can vary (below: first 5 × 5, then 3 × 3)
![[Pasted image 20240430084719.png]]

### Padding 
Fact of extending the image to fit better the filters (which are square). 
To be able to center the convolution kernel on the boundary locations, padding extends the input.
	This can be done by mirroring or copying the boundary values
	E.g., C × 3 × 5 input (in white) with a padding of (2,1)
	![[Pasted image 20240430085048.png]]
### Convolution stride 
In a standard convolution, the filter is shifted **pixel by pixel**
The shifts can be made larger by increasing the **stride** (making the **steps bigger**)
	E.g., stride of (2,2) on the previously-padded input
	![[Pasted image 20240430085249.png]]

Eventually we want to output a vector 
	a vector of class probabilities for classification
	a vector of 3D joint coordinates for human pose estimation 
This can be done by appending fully-connected layer(s), this requires **vectorizing** the last convolutional output first : 
![[Pasted image 20240430085735.png]]

## CNN : Learning
Learning the weights of the CNN layers is done in the same manner as for MLPs, using stochastic gradient descent with mini-batches. 
The same idea of backpropagation that we discussed last time is applicable to convolutional layers.

The gradient of pooling layers depends on the strategy : 
- For average pooling, the gradient is backpropagated to all neurons used during pooling
- For max pooling, the gradient is backpropagated only to the neuron that corresponded to the maximum value

### Interpretation 
A CNN can be thought of as jointly learning the data representation and the classifier (or regressor). 
![[Pasted image 20240430092435.png]]
## Standard architectures 
Since it is very complex (oulala we have to choose the number of layer, this is too much), we have some "standard models" that work well :)

 <u>LeNet-5</u>
 ![[Pasted image 20240430092946.png]]
 <u>AlexNet</u> ![[Pasted image 20240430093032.png]]
<u>VGG</u>
![[Pasted image 20240430093142.png]]

<u>ResNet</u>
![[Pasted image 20240430093243.png]]


## Transposed convolution
### Tackling different tasks 
When working with images, one does not necessarily want to simply classify them, e.g., semantic segmentation is the task of assigning a label to every pixel in the input image. 
![[Pasted image 20240430093537.png]]
The **problem**, 
	With convolutions and pooling layer, the size of the feature maps decreases at every layer.
	With the operations seen before, we have no way of increasing this size back to the original resolution
	![[Pasted image 20240430093712.png]]

The **solution**, 
	- Upsampling, followed by convolutions
	- Transposed convolutions 

We will look into : 
### Transposed convolution (yeah)

Since a convolution can be represented by a rectangular matrix going from dimension W to dimension W-w+1, increasing the dimensionality can be achieved by using the transposed matrix.  
$$\begin{bmatrix} w_1 & w_2 & w_3 & 0 & 0 & 0 \\ 0 & w_1 & w_2 & w_3 & 0 & 0 \\ 0 & 0 & w_1 & w_2 & w_3 & 0 \\ 0 & 0 & 0 & w_1 & w_2 & w_3 \end{bmatrix}^T = \begin{bmatrix} w_1 & 0 & 0 & 0 \\ w_2 & w_1 & 0 & 0 \\ w_3 & w_2 & w_1 & 0 \\ 0 & w_3 & w_2 & w_1 \\ 0 & 0 & w_3 & w_2 \\ 0 & 0 & 0 & w_3 \end{bmatrix}$$
(w_1, w_2, w_3 being the "filter of the vector")

#### 2D convolution as matrix product
A similar matrix representation can be obtained for 2D convolutions
- The input is vectorized, e.g., by concatenating its columns
- The matrix is obtained by spreading the different columns of the kernel at the matching locations
![[Pasted image 20240430094446.png]]

## Deep Learning : trick of the trade
Pre-training 
	People often start with a standard architecture trained on ImageNet
Learning rate scheduling 
	Decrease the learning rate after a pre-defined number of epochs
	Use a cyclic learning rate
	![[Pasted image 20240430095631.png]]
Data augmentation :
	E.g., for images, flip, rotate, affine transform...



