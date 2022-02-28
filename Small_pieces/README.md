# Small pieces MEMO

When to use MinMaxScalar and StandardScalar?

* MinMaxScalar:
  * well-known boundary: image
  * if there are outliers, may squeeze other values into a corner, sensitive to outliers
* StandardScalar:
  * good for some linear models
  *  standard normal distribution E=0, std=1, making it easier to learn the weights
  * still keep outliers, less sensitive to outliers



Unordered operation in Python: Set, no order, no index



Logistic regression performs better on small dataset:

* no assumption on distribution
* data is linear seperable

* Related: Linear SVC
