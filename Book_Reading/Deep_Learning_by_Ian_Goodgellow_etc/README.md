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

### Indentity and Inverse Matrices

* Identity matrices $\mathit{I}_n \in \mathbb{R}^{n\times n}$ , then $\forall x\in \mathbb{R}^n, \mathit{I}_nx = x$

* matries inverse of $A$ is $A^{-1}$, and $A^{-1} A  = \mathit{I}_n$.                                                                                                              

So we can solve the linear system by
$$
Ax = b \\
A^{-1}Ax = A^{-1}b \\
I_n x = A^{-1}b \\
x = A^{-1}b
$$
This process depends on it being possible to find $A^{-1}$.

*However, a digital computer can only find the closed form of $A^{-1}$ with limited precision, and this is the reason why this solution of such linear system is more of a theoretical tool while not actually be used in practice.*

*Algorithms that make use of the value of b can usually obtain more accurate estimates of $x$*.

### Linear dependent and Span

* *For $A^{-1}$ to exist, $Ax = b$ must has exactly one solution for every value of $b$.* 

It is possibly that $Ax = b$ have no solution or infinite solutions but impossible to have more than one but less than infinite solutions for a particular $b$, because if both $x$ and $y$ are solutions of this quation, then $z$ in the following equation should also be the solution of $Ax = b$ .
$$
z = \alpha x + (1-\alpha)y
$$
To Analyze how many solutions it has, *think of columns of A as specifying different directions we can trvel in from the origin $[0,0,...,0]^T$ to $b$.* So each element of $x$ specifying how far to move in each direction of column $i$, and this is a linear combination:
$$
Ax = \sum_i x_i A_{:,i}
$$
*This is also a span of a set of vectors, which is just columns of matrix $A$.* It is the set of all points obtainable by linear combination of the original vectors.

* If $b$ in the span of the columns of $A$, the linear system $Ax = b$ must have at least one solution.
  * This span is the column space of $A$, also called range of $A$
* In order for  $Ax = b$ has solution for any $b \in \mathbb{R}^m$, the column space of $A$ should be the whole $\mathbb{R}^m$
* Thus, $A$ must have $n\ge m$ independent columns, for at least one solution of $Ax = b$, not exatly one. 

Example, consider a $3\times2$ matrix $A$ with a $b\in \mathbb{R}^3$, since the column space of $A$ is 2-D while $b$ is 3-D, only when $b$ lies on that plane, $Ax = b$ has a solution. In this case, it is not for every $b \in \mathbb{R}^3$.

* Here we say independent columns number, not just the number of columns, since there may be some 'redundant' columns.
* The necessay and suffficient for solve $Ax = b$ is a set with exactly $m$ linear independent columns, not at least $m$. 
  * A set of m-D vectors cannot have more tham m independent columns.
  * If there is more than $m$ independent vectors, there will be infinite solution for $Ax = b$.
* Thus, the matrix mush be square. *If a squre matrix has dependent columns, it is called singular.*
  * If a matrix is not square, or square but singular, then it is invertible
  * If a matrix is invertible then it is non-singular.

* $A A^{-1} = \mathit{I}_n$.  This is called right inverse. For square matrix, left inverse and right inveres are the same.           
