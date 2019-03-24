---
title: "Machine Learning #4: Model Optimization"
date: 2019-02-01
tags: []
excerpt: "Regularization & Bagging"
encoding: UTF-8
---

---
# Regularization

It is common for test error to decrease with some added complexity but to then increase with too much added complexity as they tend to start overfitting. Regularization is used to *reduce* model complexity, mostly to reduce *over fitting*.

<figure style="width: 100%" class="align-centre">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/11-complex-error.png" alt="Complexity vs Test Error Plot">
</figure>

---
# Model Averaging

Model averaging can reduce overfitting.

Given $B$ independent training sets of equal size we can train $B$ independent prediction functions $(\hat f_1, ... \hat f_B)$.

The model average over $B$ is defined
$$\hat f_{avg} = \frac 1B \sum ^B_{i=1} \hat f_i$$

$$E[\hat f(x_0)] = \frac 1B E[\sum ^B_{i=1} \hat f_i(x_0)]$$
$$= \frac 1B \sum ^B_{i=1} E[\hat f_1(x_0)]$$
$$= E[\hat f_1(x_0)]$$

This shows that model averaging does not change the expected prediction.

$$var(\hat f_{avg}(x_0)) = var(\frac 1B \sum ^B_{i=1} \hat f_i(x_0))$$
$$ = \frac 1B var(\hat f_1(x_0))$$

This shows that model averaging can reduct the error due to variance.  

However, in real life we do **not** have $B$ independent datasets.

---
# Bagging

Bagging, also known as *bootstrap aggregation* is used when we want to decrease the model variance while retaining a level of bias. It is a special case of model averaging. First we must bootstrap the dataset, taking the average of the bootstrapped datasets is bagging.

### Bootstrapping

Bootstrapping is a term that can be applied to any process involving random sampling *with replacement* (each value returns to dataset after it's sampled).

Given a dataset of size N:

1. Repeat for i from 1:B
    - $D_i$ = randomly drawn set of points (with replacement)
2. Result is B boostrapped datasets ($D_1$, $D_2$ ... $D_B$)

### Bootstrap Aggregation

Train B prediction functions on B bootstrapped datasets: $\hat f_1, \hat f_2 ... \hat f_B$.

Take the average to reduce the variance

$$\hat f_{avg} = \frac 1B \sum^B_{i=1} \hat f_i$$

The theoretical variance is $\frac 1B var(\hat f_1(x_0))$. In practice the variance will be higher since the training sets are never totally independent. Bagging still works well when the prediction function suffers from high variance.