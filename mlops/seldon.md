# Seldon

TODO

## Monitoring

###  alibi-detect

> https://github.com/SeldonIO/alibi
> https://docs.seldon.io/projects/seldon-core/en/stable/analytics/outlier_detection.html
> https://docs.seldon.io/projects/seldon-core/en/stable/analytics/drift_detection.html
> https://docs.seldon.io/projects/alibi-detect/en/stable/examples/alibi_detect_deploy.html

Alibi Detect is an open source Python library focused on outlier, adversarial and drift detection.

![https://docs.seldon.io/projects/seldon-core/en/stable/_images/analytics.png](/Users/anton/MyDocuments/Notes/mlops/.seldon-images/analytics.png)

#### What is drift?

Although powerful, modern machine learning models can be sensitive. Seemingly subtle changes in a data distribution can destroy the performance of otherwise state-of-the art models, which can be especially problematic when ML models are deployed in production. Typically, ML models are tested on held out data in order to estimate their future performance. Crucially, this assumes that the *process* underlying the input data X and output data Y remains constant.

*Drift* is said to occur when the process underlying X and Y at test time differs from the process that generated the training data. In this case, we can no longer expect the model’s performance on test data to match that observed on held out training data. At test time we always observe features X, and the *ground truth* then refers to a corresponding label Y. If ground truths are available at test time, *supervised drift detection* can be performed, with the model’s predictive performance monitored directly. However, in many scenarios, such as the binary classification example below, ground truths are not available and *unsupervised drift detection* methods are required.

![Drift in deployment](/Users/anton/MyDocuments/Notes/mlops/.seldon-images/drift_deployment.png)

**Covariate drift**: Also referred to as input drift, this occurs when the distribution of the input data has shifted. This may result in the model giving unreliable predictions.

**Prior drift**: Also referred to as label drift, this occurs when the distribution of the outputs has shifted. This can affect the model’s decision boundary, as well as the model’s performance metrics.

**Concept drift**: This occurs when the process generating y from x has changed. It is possible that the model might no longer give a suitable approximation of the true process.

![2D drift example](/Users/anton/MyDocuments/Notes/mlops/.seldon-images/bg_2d_drift.png)

[Alibi Detect](https://github.com/SeldonIO/alibi-detect) offers a wide array of methods for detecting drift. Generally, these aim to determine whether the distribution P(z) has drifted from a reference distribution Pref(z), where z may represent input data X, true output data Y, or some form of model output, depending on what type of drift we wish to detect.

To decide whether differences between P(z) and Pref(z) are due to drift or just natural randomness in the data, *statistical two-sample hypothesis* testing is used, with the null hypothesis P(z) = Pref(z) . If the p-value obtained is below a given threshold, the null is rejected and the alternative hypothesis P(z) != Pref(z) is accepted, suggesting drift is occurring.

![Drift detection pipeline](/Users/anton/MyDocuments/Notes/mlops/.seldon-images/drift_pipeline.png)

Hypothesis testing involves first choosing a *test statistic* S(z), which is expected to be small if the null hypothesis H0 is true, and large if the alternative Ha is true.

The *test statistics* available in [Alibi Detect](https://github.com/SeldonIO/alibi-detect) can be broadly split into two categories:

- Univariate:
  - [Chi-Squared](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/chisquaredrift.html) (for categorical data)
  - [Kolmogorov-Smirnov](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/ksdrift.html)
  - [Cramér-von Mises](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/cvmdrift.html)
  - [Fisher’s Exact Test](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/fetdrift.html) (for binary data)
- Multivariate:
  - [Least-Squares Density Difference (LSDD)](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/lsdddrift.html)
  - [Maximum Mean Discrepancy (MMD)](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/mmddrift.html)

Dimension reduction: Linear and Non-linear projections.

Feature maps.

Model uncertainty detector uses the ML model of interest itself to detect drift.

Input preprocessing.

Text data - we extract contextual embeddings from language transformer models and detect drift on those. 

Graph data requires preprocessing before drift detection can be performed. This can be done by extracting graph embeddings from graph neural network (GNN) encoders.

**Learned drift detection**

- [Learned kernel](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/learnedkerneldrift.html)
- [Classifier](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/classifierdrift.html)
- [Spot-the-diff](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/spotthediffdrift.html)(erence)

It is important that the learned detectors are trained on training data which is held-out from the reference data set.

**Online drift detection**

One approach is to perform a test for drift every W time-steps, using the W samples that have arrived since the last test. If the window size  is too large the delay between consecutive statistical tests hampers responsiveness to severe drift, but an overly small window is unreliable.

An alternative strategy is to perform a test each time data arrives. However the usual offline methods are not applicable because the process for computing p-values is too expensive. Additionally, they don’t account for correlated test outcomes when using overlapping windows of test data, leading to miscalibrated detectors operating at an unknown False Positive Rate (FPR). 

Specialist online drift detectors:

- [Online Maximum Mean Discrepancy](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/onlinemmddrift.html)
- [Online Least-Squares Density Difference](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/onlinelsdddrift.html)
- [Online Cramér-von Mises](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/onlinecvmdrift.html)
- [Online Fisher’s Exact Test](https://docs.seldon.io/projects/alibi-detect/en/stable/cd/methods/onlinefetdrift.html)

The detectors compute a test statistic S(z) during the configuration phase. Then, at test time, the test statistic is updated sequentially at a low cost. When no drift has occurred the test statistic fluctuates around its expected value, and once drift occurs the test statistic starts to drift upwards. When it exceeds some preconfigured threshold value, drift is detected.

Expected run-time and detection delay - this is the number of time-steps that we insist our detectors, on average, should run for in the absence of drift, before making a false detection.

#### Outlier, adversarial and drift detection on CIFAR10

> https://docs.seldon.io/projects/seldon-core/en/stable/examples/outlier_cifar10.html
> https://docs.seldon.io/projects/seldon-core/en/stable/examples/drift_cifar10.html
> https://docs.seldon.io/projects/seldon-core/en/stable/examples/cifar10_od_poetry.html


- Train a VAE on *normal* data so it can reconstruct *inliers* well
- If the VAE cannot reconstruct the incoming requests well? Outlier!

![image-20231113114504941](/Users/anton/MyDocuments/Notes/mlops/.seldon-images/image-20231113114504941.png)

The adversarial detector is based on [Adversarial Detection and Correction by Matching Prediction Distributions](https://arxiv.org/abs/2002.09364).

![image-20231113115244426](/Users/anton/MyDocuments/Notes/mlops/.seldon-images/image-20231113115244426.png)

