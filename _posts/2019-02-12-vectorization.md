---
title: Matlab/Octave 向量化
categories: ['机器学习']
tags: [Matlab, Octave]
resource_path: /blog/2019/02/12/vectorization
---

量化的优点
---
1. **高效**。经过相关专业人士的优化，其执行速度一般会比自己写的代码的执行速度快很多。
2. **简单**。通过直接调用库来简化代码。

一个简单的例子
---
* 线性回归假设函数（hypothesis for linear regression）：
$$
h_{\theta}(x) = \sum_{j=0}^n{\theta}_{j}x_{j}
\\=\theta^Tx
$$

* 非向量化的实现

```MATLAB
prediction = 0.0
for j = 1:n+1  % matlab中向量下标从1开始
  prediction = predictrion +
               theta(j) * x(j)
end;
```

* 向量化的实现
  
  设
  $\Theta =
   \begin{bmatrix}
   {\theta_0}\\
   {\theta_1}\\
   {\theta_2}
   \end{bmatrix}
  $
  ,
  $
  X =
  \begin{bmatrix}
  {x_0}\\
  {x_1}\\
  {x_2}
  \end{bmatrix}
  $
  (matlab中下标从1开始)
```MATLAB
prediction = theta' * x;
```

一个较为复杂的例子
---
* 一个线性回归问题的梯度下降的更新方法(the update rule for a gradient descent of a linear regression)
  $$ \theta_j \colonequals \theta_j - \alpha \frac{1}{m} \sum_{i=1}^m(h_\theta(x^ {(i)}) - y^{(i)})x_j^{(i)}  \quad (for \ all \ j)$$
  注意 $\theta_0$, $\theta_1$,$\theta_2$ ... 是同时更新的

* 向量化的实现：
  $$
  \Theta \colonequals \Theta - \alpha \delta \\
  \Theta = {[\theta_0,\, \theta_1, \, \dots\, \theta_n]}^T \\
  \delta = \frac{1}{m} \sum_{i=1}^m(h_{\theta}(x^{(i)}-y^{(i)})x^{(i)}\\
  \delta =
  $$
   
```MATLAB
```

---
[课程链接](https://www.coursera.org/learn/machine-learning/lecture/WnQWH/vectorization)