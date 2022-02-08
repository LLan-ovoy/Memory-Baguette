# Deep Learning

This is the reading summary of [Deep Learning](https://www.amazon.com/Deep-Learning-Adaptive-Computation-Machine/dp/0262035618/ref=asc_df_0262035618/?tag=hyprod-20&linkCode=df0&hvadid=312128454859&hvpos=&hvnetw=g&hvrand=1227255401146736030&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9061268&hvtargid=pla-416263148149&psc=1) by Ian Goodfellow, Yoshua Bengio and Aaron Courville.

### Introduction

##### Why deep learning? 

* To let computer system learn the complex real-world environment,  which actually is learned by human brain without efforts. Machine Learning is the only way to do this, while deep learning is one type of machine learning.

##### What is deep learning?

* A type of machine learning, a technique enables computer systes to improve with experience and data. It represents the world by nested hierarchy of concepts, which each concept deifined in relation to simpler concepts and more abstract representations computed in terms of less abstract ones.

##### The history of deep learning

* Cybernetic
* Connectionism 
* Neural Networks



## Review of Linear Algebra

> I will only listed some concepts here unless those I am not familiar with, denoted in *italic*.

##### Mathematiclal objects

* Scalars

* Vectors

* Matrices

* Tensors

  * *An array with more than two axes, i.e. variable number of axes.*

  

#### operation

* transpose $A^T_{i,j} = A_{j,i}$, *can also think of vectors and scalars as special kind of matrices*
  * vector dot product: $x^T y = y^T x = (x^T y)^T$
* addtion $C = A+B$
* scalar multiplication $D = a \cdot B +c$
* *less conventional notation in DL - broadcasting*: $C = A+b \Rightarrow C_{i,j} = A_{i,j} + b_j$
* marix product $C = AB \Rightarrow C_{i,j} = \sum_{k} {A_{i,k}B_{k,j}}$
  * $A(B+C) = AB +AC$
  * $A(BC) = (AB)C$
  * $(AB)^T = B^TA^T$
* element-wise product/ Hadamard product $A \odot B$



### A system of linear equations

A is a metric, x is a vector and b is an vector,
$$
Ax = b
$$
In which,
$$
A_{1,:} x =b1
$$
more detailed,
$$
A_{1,1}x_1 + A_{1,2}x_2 + ... + A_{1,n}x_n = b1
$$

#### Indentity and Inverse Matrices

* Identity matrices $\mathit{I}_n \in \mathbb{R}^{n\times n}$ , then $\forall x\in \mathbb{R}^n, \mathit{I}_nx = x$

* matries inverse of $A$ is $A^{-1}$, and $ A A^{-1} = \mathit{I}_n$
