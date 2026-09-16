# Machine Learning Crash Course

## Linear Regression
The equation for linear regression is, y = b + w.x
- y is the predicted label value
- b is the bias of the model similar to the y-intercept and is calculated during training.
- w is the weight of the feature similar to the slope m and is calculated during training.
- x is the feature

Linear regression model plot a striaght line to spot trends and make predictions.

### Loss
Loss measures the distance between the model's predictions and the actual labels. The goal of training a model is to minimize the loss, reducing it to its lowest possible value.

There are four main types of losses:
- **L1 loss**: The sum of the absolute values of the difference between the predicted values and the actual values. 
- **MAE (Mean Absolute Error)**: The average of L1 losses across a set of N examples. 
- **L2 loss**: The sum of the squared difference between the predicted values and the actual values.
- **MSE (Mean Square Error)**: The average of L2 losses across a set of N examples.

- MSE: The model is closer to the outliers but further away from most of the other data points.
- MAE: The model is further away from the outliers but closer to most of the other data points.

### Gradient Descent
Gradient descent is a mathematical technique that iteratively finds the weights and bias that produce the model with the lowest loss. Gradient descent finds the best weight and bias by repeating the following process for a number of user-defined iterations.

The model begins training with randomized weights and biases near zero, and then repeats the following steps:
- Calculate the loss with the current weight and bias.
- Determine the direction to move the weights and bias that reduce loss.
- Move the weight and bias values a small amount in the direction that reduces loss.
Return to step one and repeat the process until the model can't reduce the loss any further.

If we graph the loss surface for a model with respect to the weight and bias, we can see its convex shape.
As a result of this property, when a linear regression model converges, we know the model has found the weights and bias that produce the lowest loss.

So the term gradient descent basically comes from the weights and biases descending the plan formed by the loss and eventually reaching the lowest possible loss value and the best accuracy.

### Hyperparameters
Hyperparameters are variables that control different aspects of training. Three common hyperparameters are:
- **Learning rate** determines the magnitude of the changes to make to the weights and bias during each step of the gradient descent process.
- **Batch size** refers to the number of examples the model processes before updating its weights and bias. 
    - *Standard (or Batch) Gradient Descent* uses the entire dataset to compute the gradient for each parameter update, leading to slow, accurate, and stable convergence
    - *Stochastic Gradient Descent (SGD)* uses a single random data point or a small subset (mini-batch) per update, resulting in faster, noisier, and potentially less accurate but more generalized convergence
- **Epochs** number of times the model has processed every example in the training set.

## Logistic Regression

Logistic regression is a supervised machine learning and statistical algorithm used for predicting the probability of a binary (two-class) outcome.
It makes sure we get a value between 0 to 1 using a sigmoid function for mapping. 

### Log Loss
The Log Loss equation returns the logarithm of the magnitude of the change, rather than just the distance from data to prediction.

## Concepts

**Accuracy**
The proportion of all classifications that were correct, whether positive or negative. It is mathematically defined as:
`Accuracy = (TP+TN) / (TP+TN+FP+FN)`

**Recall** or true positive rate
The proportion of all actual positives that were classified correctly as positives, is also known as recall. Recall is mathematically defined as:
`Recall = TP / (TP + FN)`

**Precision**
The proportion of all the model's positive classifications that were actually positive.
`Precision = TP / (TP + FP)`

**ROC and AUC**
The ROC curve is a visual representation of model performance across all thresholds. It is drawn by calculating th true positive rate and false positive rate at every possible threshold.
The area under the ROC curve represents the probability that the model, if given a randomly chosen positive and negative example, will rank the positive higher than the negative

**Normalization**
It transforms the features so that they end up on a similar scale
- Linear Scaling - convert from its natural range to a standard 0 to 1 or -1 to 1 range,
- Z-Score Scaling - A Z-score is the number of standard deviations a value is from the mean and is used to represent the value.
- Logarithmic Scalind - calculates the logarithmi of the raw value.

**Encoding**
Encoding means converting categorical or other data to numerical vectors that a model can train on. This conversion is necessary because models can only train on floating-point values; models can't train on strings such as "dog" or "maple".

## Neural Networks

### Activation Function
It allows neural networks to learn non-linear between features and the labels. They are mainly of 3 types -
- **Sigmoid** - transforms the input value producing an output value between 0 and 1
- **TanH** (hyperbolic tangent) - transforms input to produce an output value between –1 and 1
- **ReLU** (rectified linear unit) - F(x) = max(0,x), basically the number below 0 are mapped to 0

### Training Practices
- **Vanishing Gradients** - The gradients for the lower neural network layers can become very small. In deep networks, computing these gradients can involve taking the product of many small terms.
    - When the gradient values approach 0 for the lower layers, the gradients are said to "vanish". Layers with vanishing gradients train very slowly, or not at all.
    - The **ReLU** activation function can help prevent vanishing gradients.
- **Exploding Gradients** - If the weights in a network are very large, then the gradients for the lower layers involve products of many large terms. In this case you can have exploding gradients: gradients that get too large to converge.
    - **Batch normalization** can help prevent exploding gradients, as can lowering the learning rate.
- **Dead ReLU Units** - Once the weighted sum for a ReLU unit falls below 0, the ReLU unit can get stuck. It outputs 0, contributing nothing to the network's output, and gradients can no longer flow through it during backpropagation.
    - Lowering the learning rate can help keep ReLU units from dying.

### Embedding
It is the vector representation of data in embedding space. Embedding makes it easier to do machine learning on large feature vectors.
