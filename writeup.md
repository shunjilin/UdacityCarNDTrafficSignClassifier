# **Traffic Sign Recognition** 

Shunji Lin

---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./writeup_images/image1.png "Sample training images"
[image2]: ./writeup_images/image2.png "Barplot of training images"
[image3]: ./writeup_images/image3.png "Rotation"
[image4]: ./writeup_images/image4.png "Swirl"
[image5]: ./writeup_images/image5.png "Grayscale"
[image6]: ./writeup_images/image6.png "Normalization"
[image7]: ./writeup_images/image7.png "Preprocessed sample training images"
[image8]: ./writeup_images/image8.png "Barplot preprocessed"
[image9]: ./writeup_images/image9.png "Traffic Signs"


### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf. You can use this template as a guide for writing the report. The submission includes the project code.

You're reading it! and here is a link to my [project code](https://github.com/shunjilin/UdacityCarNDTrafficSignClassifier/blob/master/Traffic_Sign_Classifier.ipynb)

### Data Set Summary & Exploration

#### 1. Provide a basic summary of the data set. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

Summary statistics of the traffic signs data set:

* The size of training set is 34799
* The size of the validation set is 4410
* The size of test set is 12630
* The shape of a traffic sign image is (32, 32, 3)
* The number of unique classes/labels in the data set is 43

#### 2. Include an exploratory visualization of the dataset.

Exploratory visualization of the data set.

Sample plot of random images from each class (training set):

![alt text][image1]

We can see that some images are significantly darker/brighter than others.

Bar plot of class distribution (training set):

![alt text][image2]

We can see an uneven distribution of class samples.

### Design and Test a Model Architecture

#### 1. Describe how you preprocessed the image data. What techniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, and provide example images of the additional data. Then describe the characteristics of the augmented training set like number of images in the set, number of images for each class, etc.)

I performed data augmentation to balance the number of images per class for the training set.

For each class, I ensured that there is a minimum of 1500 samples per class.

Each augmented data is taken randomly from the original class, and distorted with transformations from the scikit-image package:

1. Random rotation of -5 to 5 degrees

![alt text][image3]

2. Swirl of radius 10 and strength 1

![alt text][image4]

I performed preprocessing of all data in the training, validation and test set, which includes:

1. Grayscale conversion

![alt text][image5]

Grayscale to compress the dataset

2. Normalization

![alt text][image6]

Normalization for better training efficiency, and also to balance the level of contrast/brightness in the dataset.

Sample plot of random images from each class after augmentation / preprocessing (training set):

![alt text][image7]

Bar plot of class distribution after augmentation / preprocessing (training set):

![alt text][image8]

#### 2. Describe what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

The final model consisted of the following layers:

| Layer         	|     Description				| 
|:---------------------:|:---------------------------------------------:| 
| Input         	| 32x32x1 preprocessed image   			| 
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 28x28x6 	|
| RELU			|			     	     		|
| Max pooling 2x2    	| 2x2 stride, outputs 14x14x6 			|
| Convolution 5x5	| 1x1 stride, valid padding, outputs 10x10x16	|
| RELU	      		|     	      	    	     	     		|
| Max pooling 2x2	| 2x2 stride, outputs 5x5x16			|
| Flatten     		| Combine and flatten 1st convolution output (after RELU) with second convolution output (after RELU), outputs 1576 |
| Dropout		| keep_prob = 0.5     	  	      	     	|
| Fully connected	| outputs 120 					|
| RELU			|						|
| Dropout		| keep_prob = 0.5				|
| Fully connected	| outputs 84  					|
| RELU			| 	      					|
| Dropout		| keep_prob = 0.5				|
| Fully connected	| outputs 43 (n_classes)			|
| Softmax		|						|
 


#### 3. Describe how you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

Parameters to train the model:
* Initialize data with truncated normal of mean 0 and standard deviation of 0.1
* 100 epochs
* batch size of 128
* Adam Optimizer with learning rate of 0.001

#### 4. Describe the approach taken for finding a solution and getting the validation set accuracy to be at least 0.93. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

My final model results were:
* training set accuracy of 99.8%
* validation set accuracy of 97.7%
* test set accuracy of 95.3%

If an iterative approach was chosen:
* What was the first architecture that was tried and why was it chosen?
Plain LeNet, as per the MNIST tutorial, with < 20 epochs, and no data augmentation.

* What were some problems with the initial architecture?
Was not able to get a good validation accuracy.

* How was the architecture adjusted and why was it adjusted? Typical adjustments could include choosing a different model architecture, adding or taking away layers (pooling, dropout, convolution, etc), using an activation function or changing the activation function. One common justification for adjusting an architecture would be due to overfitting or underfitting. A high accuracy on the training set but low accuracy on the validation set indicates over fitting; a low accuracy on both sets indicates under fitting.
I referred to the implementation in the paper "Traffic Sign Recognition with Multi-Scale Convolutional Networks", and attempted the multi-scale implementation whereby I combined the output of the 2 convolutional layers (after activation) before feeding them to the fully connected layer. I also implemented dropout on each of the fully connected layers.

* Which parameters were tuned? How were they adjusted and why?
Initially, I used quite significant distortions in terms of rotation and swirl, and my validation accuracy suffered. I limited the distortions and achieved better results. I also increased the number of epochs to 100.

* What are some of the important design choices and why were they chosen? For example, why might a convolution layer work well with this problem? How might a dropout layer help with creating a successful model?
For multi-scale features, according to the authors of the paper, the motivation is 'to provide different scales of receptive fields to the classifier'. Dropout is used as a form of regularization to prevent the network from overfitting the training data.

If a well known architecture was chosen:
* What architecture was chosen?
* Why did you believe it would be relevant to the traffic sign application?
* How does the final model's accuracy on the training, validation and test set provide evidence that the model is working well?
The LeNet architecture has been tried and tested for multi-class image classification, and therefore was used.
The high accuracies of the training, validation and test sets (> 95%) prove that the model is effective for this use case.
 

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Here are five German traffic signs that I found on the web (after resizing):

![alt text][image9]

The 'Bicycles crossing' sign might be difficult to classify as it is in a circular form as opposed to the usual triangular form. The 'Roadwork' sign might be difficult to classify as there is significant noise in the background. The 'Pedestrian' sign might be difficult to classify as it is taken at a sharp angle, and the sign is significantly tilted. The other two signs, 'Roundabout mandatory', and 'Right-of-way at the next intersection' are standard looking.

#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			|     Prediction				| 
|:---------------------:|:---------------------------------------------:| 
| Bicycles crossing    					| Turn left ahead | 
| Road work     					| Beware of ice/snow |
| Roundabout mandatory					| Roundabout mandatory |
| Pedestrians	      					| Speed limit (30km/h) |
| Right-of-way at the next intersection			| Right-of-way at the next intersection |

The model was able to correctly guess 2 of the 5 traffic signs, which gives an accuracy of 40%. This is low but understandable given the difficulties explained previously.

#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

For the above 5 images, the model is very certain for all of them ( > 90% for the top choice classification ) with the exception of the image of 'Pedestrian'. The top 5 softmax probabilites for this image were:

| Probability         	|     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| .493         			| Speed limit (30km/h)   									| 
| .298     				| Speed limit (50km/h) 										|
| .065					| Speed limit (20km/h)											|
| .054	      			| Road work					 				|
| .026				    | Go straight or left      							|

The actual classification of 'Pedestrian' is not included in the top 5 probabilities!

### (Optional) Visualizing the Neural Network (See Step 4 of the Ipython notebook for more details)
#### 1. Discuss the visual output of your trained network's feature maps. What characteristics did the neural network use to make classifications?

In progress.
