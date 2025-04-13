## Bayesian vs Frequentist

Imagine I have misplaced my phone somewhere in the home. I can use the phone locator on the base of the instrument to locate the phone and when I press the phone locator the phone starts beeping. What area of my home should I search?

**Frequentist:**

I can hear the phone beeping, and I also have a mental model which helps me identify the area from which the sound is coming. Thus, upon hearing the beep, I infer the area of my home I must search to locate the phone.

**Bayesian:**

I can hear the phone beeping. Apart from my mental model, I also know the locations where I have misplaced the phone in the past. So, I combine my inferences using the beeps and my prior info about the locations I have misplaced in the past to identify an area I must search to locate the phone.

Bayesian stats assign probability to a specific outcome; probability is subjective, based on personal belief = allows us to assign probabilities to very infrequeny events and events that have not been observed before.

Bayesian yields probability distributions vs frequentist inference focuses on point estimates. Parameters are assigned a probability whereas in the frequentist approach, the parameters are fixed. In frequentist statistics, we take random samples from the population and aim to find a set of fixed parameters that correspond to the underlying distribution that generated the data. In contrast for Bayesian statistics, we take the entire data and aim to find the parameters of the distribution that generated the data but we consider these parameters as probabilities i.e. not fixed.

| Bayesian                            | Frequentist                                                  |
| ----------------------------------- | ------------------------------------------------------------ |
| Credible interval, prior, posterior | Confidence Interval, p-value, power, significance            |
| Opinions to have (prior belief)     | Actions to take                                              |
| Intuitive definitions               | Makes sense to talk about method's quality and getting the answer right |
| The parameter IS a random variable  | parameters is NOT a random variable                          |

Bayesian hierarchical models:

1. Data is organized into a hierarchical structure
2. Each observation is part of a group and each group has a distribution
3. For each observation, a prediction is based on a combination of predictions for each group that it is a member of

A conjugate prior is when the posterior distribution is in the same probability distribution family as the prior probability distribution.

---

## Bias Variance

We say that a model has a *high bias* if it is not able to fully use the information in the data. It is too reliant on general information, such as the most frequent case, the mean of the response, or few powerful features. Bias can come from wrong assumptions, for example assuming that the variables are normally distributed or that the model is linear.

We say that a model has *high variance* if it is using too much information from the the data. It relies on information that is revelant only in the training set that has been presented to it, which does not generalize well enough. Typically, the model will change a lot if you change the training set, hence the "high variance" name.

**Tradeoff between bias and variance**

- If a model is too simple then it has high bias and low variance

- Models that are too large have high variance and low bias

To improve underfitting:

- add new features, change types of feature processing

- decrease amount of regularization used

To improve overfitting:

- feature selection, use fewer features or bins

- increase amount of regularization used

**Levels of Error**

1. Total error - the overall difference between model predictions and the actual data. Defined as performance metrics such as Mean Squared Error (MSE)
2. Bayes Optimal Error - best error that can be achieved
3. Human level error - error achieved by humans on tasks
   - Difference (Training Error, Human-level performance) = Avoidable bias
   - Difference (Development Error, Training Error) = Variance

4. Bayes Error
   - Avoidable bias (training bigger NN, running training longer, better optimization)

5. Training set error
   - Variance (regularization, bigger dataset, hyperparameters)

6. Development - Training set error
   - Data mismatch

7. Development set error
   - Overfitting

8. Test set error

---

# Neural Nets

### Activation Function

1. Sigmoid: range around (-1, 1)
   - Suffers from the vanishing gradient problem due to not centered around 0


2. Tanh: range around (-1, 1)

   - Better for gradients since centered around 0


   - Tanh vs Sigmoid: Activation function of tanh is better since derivaties are larger than sigmoids, because centered around 0


3. ReLU: max(0, x)

   - One of the best empirical

   - ReLU vs Sigmoid: fewer neurons are firing and network is lighter

4. Leaky ReLU: maX(-small num, x)
   - Prevent dying ReLU problem by having a small slope


### Backprogation

1. Take a batch of training data
2. Perform forward propagation to obtain the corresponding loss
3. Backpropagate the loss to get the gradients (sends information backwards through NN)
4. Use the gradients to update the weights of the network

### Dropout

Dropout is a technique to prevent overfitting the training data by dropping out units in a neural network, with some probability.

### NLP Models

1. Skipgram - used from predicting target words based on a context word
2. CBOW - used by predicting target word from context words
3. Negative sampling - idea of using binary classifiers to predict whether or not the words are likely to appear simultaneously, i.e. from teddy -> bear to (teddy, bear) -> 1 or 0

### Definitions

1. Epoch - epoch is a term used to refer to one iteration where the model sees the whole training set to update its weights
2. Cross-entropy loss, similar to log-loss function
3. Convolution is the mathematical combination of two functions to produce a third function
4. Stride is the size of the step the convolution filter moves each time

---

# Optimizers

1. Gradient Descent
   - Compute the slope that is the first-order derivative
   - Move in the opposite direction of the slope increase by x amount
2. Batch Gradient Descent
   - All the training data is taken into consideration in a single step
   - Batch GD performs model updates at the end of each training epoch
   - Average of gradients of all the training examples and use the mean gradient to update parameters
   - Smoother curves, coverges to minima
3. Stochastic Gradient Descent
   - Consider one example at a single time to take a single step a. Take an example b. feed to NN c. calculate gradient d. use gradient to update weights
   - Will eventually decrease, and fluctuate quite a bit
   - For larger dataset, converges
   - Can be more computationally expensive
   - Generally efficient in the early training since gradients point in the same direction
   - Bounce from local optima
4. Mini-Batch Gradient Descent
   - Try to find a balance between the robusness of stochastic gradient descent and efficiency of batch gradient descent
   - Batch a fixed number of training examples less than the actual dataset For each epoch, a. pick a minibatch b. feed to NN c. calculate mean gradient d. use mean gradient to update weights e. repeat
   - Vectorized implementation for faster computation
5. Momentum Based Gradient Descent
6. Adam
   - The algorithm calculates an exponential moving average of the gradient and the squared gradient, and the parameters beta1 and beta2 control the decay rates of these moving averages.
   - Adam uses average of second moments of the gradients
   - AdaGrad and RMSProp

---

# Common ML

### Confidence Intervals

For a given confidence x, a confidence interval will x% of the time contain the true mean.

### KNN

Compute the K nearest data points using a certain distance metric (Euclidean metric), majority label for classification and mean for regression * K = 1 or other smaller number, model is prone to overfitting (high variance) * K = 99 or other larger number, model is prone to underfitting (high bias)

### Sampling

Sampling due to unbalanced classes.

Disadvantages:

1. Discarding useful information
2. False precision
3. Increasing computational burden (oversampling)
4. Introducing bias
5. Possible overfitting

### Parametric and Non-Parametric

Parametric data follows a probability distribution. Non-parametric data does not assume anything about the nuderlying data.

Parametric functions:

1. Exponential distribution
2. Weibull
3. Gamma
4. Poisson

Nonparametric:

1. Kaplan-Meiter
2. Nelson-Aalen cumulative hazard rate

### Generative vs Discriminative

Given a training set, an algorithm like logistic regression or the perceptron algorithm (basically) tries to find a straight line—that is, a decision boundary—that separates the elephants and dogs. Then, to classify a new animal as either an elephant or a dog, it checks on which side of the decision boundary it falls, and makes its prediction accordingly.

Here’s a different approach. First, looking at elephants, we can build a model of what elephants look like. Then, looking at dogs, we can build a separate model of what dogs look like. Finally, to classify a new animal, we can match the new animal against the elephant model, and match it against the dog model, to see whether the new animal looks more like the elephants or more like the dogs we had seen in the training set.

Based on learning shared [here](https://mmuratarat.github.io/2019-08-23/generative-discriminative-models).

---

# Embeddings

1. An Embedding is a matrix in which each column is the vector that corresponds to an item in your vocabulary.
2. An embedding is usually associated with an entity, which we’ll say is an instance of some discrete type of interest such as a user, Tweet, author, word, or phrase.

- A static embedding is an embedding of entities such that every entity has one and only one embedding value.
- A dynamic embedding, on the other hand, is an embedding of entities such that each entity can have more than one embedding value.

PCA can be useful for the creation of embeddings.

---

# Linear Methods

### SVM

- Can perform linear, nonlinear, or outlier detection (unsupervised)
- large margin classifier, boundary to be as far from the closest training point as possible

Advantages:

1. Deals with non-linearity in low dimensions
2. Can custom define kernel

Disadvantages:

1. Can be slow if many samples
2. High algorithmic complexity and memory
3. Kernels are difficult / can be hard to choose
4. If features are greater than samples, can perform poorly

---

# LSTM

### RNN

RNN vanishing and exploding gradients, which means model is biased towards most recent inputs in the sequence.

Bottleneck problem, cannot capture all of the information about the source sentence

### LSTM

LSTM/GRUs solve this problem by including a separate memory cell and/or extra gates to learn when to let go of past/current information.

### Transformers

Transformer architecture, each step direct access to all of the other steps via self-attention, leaves no room for information loss. Can look at both future and past elements at the same time with training in parallel.

Disadvantage: costly to apply on long sequences

- Non sequential, sentences are processed as a whole
- Self attention, unit to compute similairty scores between words in a sentence
- Positional Embeddings, use fixed or learned weights to encode information related to specific position

---

# Metrics

### Classification Metrics:

1. Accuracy is defined as the proportion of examples that were classified correctly. This is an appropriate metric to use for a dataset where the positive and negative classes are equally balanced, but it can be misleading if the dataset is imbalanced.
2. Precision is the proportion of examples predicted to be in the positive class that were classified correctly. So if a classifier has high precision, most of the examples it predicts as belonging to the positive class do indeed belong to the positive class.
3. Recall is the proportion of examples where the ground truth is positive that the classifier correctly identified. So if a classifier has high recall, it correctly identifies most of the examples that are truly in the positive class.
4. F1 score is the harmonic mean of precision and recall.
5. ROC AUC measures the ability of the model to distinguish between positive and negative classes across various threshold values.
6. Log Loss evaluates the quality of probabilistic predictions for classification problems.

- Precision is more important when FP are more important than FN - i.e false alarms are more costly than overlooked cases
- Recall is more important when FN are more important than FP - i.e. overlooked cases are more important than false alarms

### Regression Metrics:

1. Mean Absolute Error - measures the average absolute differences between actual and predicted values.
2. Mean Squared Error - measures the average squared differences between actual and predicted values.
3. R Squared - measures proportion of variance in dependent variable that the model explains

### Natural Language Processing Metrics:

1. BLEU Score measures the quality of machine-translated text by comparing it to human translations.
2. ROUGE Score evaluates the quality of machine-generated text by comparing it to reference texts.
3. Perplexity measures how well a language model predicts a sample text by assessing how surprised it is by the text.

---

# Over & Underfitting

### Dimensionality

1. Manual Feature Selection
2. PCA
3. Multidimensional Scaling
4. Local Linear embedding

### Ways to reduce overfitting

1. Keep the model simpler: reduce variance by taking into account fewer variables and parameters, thereby removing some of the noise in the training data.
2. Use cross-validation techniques such as k-folds cross-validation.
3. Use regularization techniques such as LASSO that penalize certain model parameters if they’re likely to cause overfitting.
4. Get more data.
5. Ensemble learning.

### Ways to reduce underfitting

1. Increase # of features in dataset
2. Choose a more complicated model(s)
3. Reduce noise in data
4. Train for longer

### Bias-Variance Tradeoff

- Overfitting = High Variance = deeper trees
- Underfitting = High bias = shallow trees

Discussion on the bias variance tradeoff found [here](https://stats.stackexchange.com/questions/204489/discussion-about-overfit-in-xgboost).

### Correlated Features

Correlated features are a problem due to possible errors due to multicollinearity, as well as making it prone to overfitting.

Techniques:

1. Clustering features
2. Eliminate variables using correlation matrices
3. Omit variables based on VIFs or PCA
4. Remove xxx number of predictors to maintain low pairwise correlation
5. Stepwise regression

---

# Stats

### Definitions

- Parameter vs statistic

Mean, SD are parameters while samples are called statistics

- Central Limit Theorem

For large enough population, it takes sufficiently large random samples from population with replacement then the distribution of the sample means will be approximately normally distributed

- Hypothesis Testing

Hypothesis testing is a statistical analysis using sample data to assess mututually exclusive theories about the properties of a population

- Type 1 Error

Error happens when we fail to accept the null hypothesis meaning we reject the null hypothesis when it should be acccepted

- Significance level

Probability of committing a $T_{1}$ error

- Type 2 Error

Fail to reject the null hypothesis, meaning we accept the null hypothesis when it should be rejected.

- P-value

Probability of finding a more extreme event given that the null hypothesis is true.

- Standardization

Standardization assumes that the values in the column are normally distributed

- Normalization

Normalization is a good technique to use if someone doesn't know the distribution of the input column or the distribution is not Gaussian

---

# Trees

## Random Forest and GBT

### Decision Tree

- Decision tree algorithms divide feature space into regions, for inference see which region does the test data point fall in, and take the mean values or the majority label
- Non parametric, supervised learning algorithms
- Construction: top-down, gini impurity of information gain

Gini impurity is the probability of incorrectly classifiying a randomly chosen element in the dataset if it were randomly labeled according to the class distribution in the dataset.

Algorithm:

1. Select attribute or value along dimension that gives best split based on gini.
2. Create child nodes based on split.
3. Recurse on each child until stopping criterion is reached:
   - all examples have same class
   - amount of data is too small
   - tree too large

### Random Forest

Algorithm Build trees with a subset of data and variables and splitting at nodes.

Advantages:

1. Accounts for interactions between features
2. Not prone to overfitting
3. Pruning not as necessary
4. Robust against outliers
5. Tends to be accurate

Random Forest use fully grown decision trees (low bias, high variance). Trees are uncorrelated to max decrease in variance.

### GBT

Explanation:

- GBT creates an ensemble of weak learners, typically decision trees with various depths
- Built in a stage-wise fashion, iteratively, using boosting methods

Gradient boosting involves three elements:

1. A loss function to be optimized.
2. A weak learner to make predictions.
3. An additive model to add weak learners to minimize the loss function.

Algorithm:

1. Chooses values in negative gradient direction iteratively
2. Create new residuals based on squared error loss

Advantages:

1. Doesn't expect features that interact linearly due to decision trees
2. Can find higher order interactions
3. Can deal with high dimensional spaces
4. Deals with large number of training data
5. Accuracy overall tends to outperform other methods

Disadvantages:

1. Prone to overfitting
2. Can be expensive to train due to iterative nature

Boosting is based on weak learners (high bias, low variance).

Links:

- Explanation on GBT [here](http://arogozhnikov.github.io/2016/06/24/gradient_boosting_explained.html)
- Random forest vs GBT found [here ](https://www.quora.com/What-are-the-differences-between-Random-Forest-and-Gradient-Tree-Boosting-algorithms)and [here](https://stats.stackexchange.com/questions/173390/gradient-boosting-tree-vs-random-forest)

### Bagging vs Ensemble Trees

|          | Bagging                    | Dropout                      |
| -------- | -------------------------- | ---------------------------- |
| Models   | Models are all independent | Subnetworks share parameters |
| Training | All models are trained     | Only fraction are trained    |
