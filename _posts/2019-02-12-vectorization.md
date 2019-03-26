---
title: 向量化
categories: ['Matlab/Octave']
tags: [Matlab, Octave]
resource_path: /blog/2019/02/12/vectorization
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

向量化的优点
---

* **高效**。  
  经过相关专业人士的优化，其执行速度一般会比自己写的代码的执行速度快很多。
* **简单**。  
  通过直接调用库来简化代码。

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
  
  设 $$ \Theta = \begin{bmatrix}
   {\theta_0} \\
   {\theta_1} \\
   {\theta_2}
   \end{bmatrix} $$
  , $$ X =
  \begin{bmatrix}
  {x_0}\\
  {x_1}\\
  {x_2}
  \end{bmatrix}$$
  (matlab中下标从1开始)

```MATLAB
prediction = theta' * x;
```

一个较为复杂的例子
---
* 一个线性回归问题的梯度下降的更新方法(the update rule for a gradient descent of a linear regression)

  $$
   \theta_j := \theta_j - \alpha \frac{1}{m} \sum_{i=1}^m(h_\theta(x^ {(i)}) - y^{(i)})x_j^{(i)}  \quad (for \ all \ j)
  $$

  注意 $$\theta_0$$,$$\theta_1$$,$$\theta_2$$ ... 是同时更新的

* 向量化的实现：
  $$
  \Theta := \Theta - \alpha \delta \\
  \Theta = {[\theta_0,\, \theta_1, \, \dots\, \theta_n]}^T \\
  \begin{align}
  \delta &= \frac{1}{m} \sum_{i=1}^m(h_{\theta}(x^{(i)})-y^{(i)})x^{(i)} \\
  &=
  \begin{bmatrix}
  \delta_0 \\
  \delta_1 \\
  \delta_2 \\
  \end{bmatrix}
  \end{align}\\ 
  \delta_0 = \frac{1}{m}\sum_{i=1}^m( h_{\theta}(x^{(i)})-y^{(i)}) \, x_0^{(i)} 
  $$
  注意 $$ x^{(i)} $$ 是向量，$$h_{\theta}(x^{(i)})$$ 和 $$y^{(i)}$$ 是标量

---
[课程链接](https://www.coursera.org/learn/machine-learning/lecture/WnQWH/vectorization)
