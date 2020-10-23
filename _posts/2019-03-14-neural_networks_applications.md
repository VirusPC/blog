---
title: 神经网络:应用 
categories: ['机器学习']
tags: ['神经网络']
resource_path: /blog/assets/2019/03/14/applications
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

神经网络的应用
===

---

非线性二分类例子：AND
---

* 数学表达式：  
  $$x_1, x_2 \in \{0,1\}\\
  y = x_1 \; AND \; x_2$$
* 图示：  
  $$
  \begin{bmatrix}
  x_0\\ x_1\\ x_2\\
  \end{bmatrix}
  \rightarrow
  [g(z^{(2)})]
  \rightarrow
  h_\theta(x)
  $$  
  ![AND]({{page.resource_path}}/and.png)

非线性二分类例子：XNOR(异或)
---

* 数学表达式：  
  $$ x_1,x_2 \in \{0,1\} \\
  y = (x_1\;AND\;x_2)\;OR\;[(NOT\;x_1)\;AND(NOT\;x_2)]$$
* 图示  
  $$
  \begin{bmatrix}
  x_0\\ x_1\\ x_2
  \end{bmatrix}
  \rightarrow
  \begin{bmatrix}
  a_1^{(2)}\\
  a_2^{(2)}
  \end{bmatrix}
  \rightarrow
  \begin{bmatrix}
  a^{(3)}
  \end{bmatrix}
  \rightarrow
  h_\theta(x)
  $$
  ![XNOR]({{page.resource_path}}/xnor.png)

多分类
---

* 在神经网络中进行多分类的算法实际上是对一对多算法的扩展
* 我们不再采用$$y \in \{1,2,3,4\}$$这种形式来表示预测结果，而是以向量的方式来表示预测结果。
  $$ y,h_\theta(x)\;\in\;
  \{\begin{bmatrix}
  1\\ 0\\ 0\\ 0
  \end{bmatrix},
  \begin{bmatrix}
  0\\ 1\\ 0\\ 0
  \end{bmatrix},
  \begin{bmatrix}
  0\\ 0\\ 1\\ 0
  \end{bmatrix},
  \begin{bmatrix}
  0\\ 0\\ 0\\ 1
  \end{bmatrix}\}
  $$
  $$
  $$
  $$
* 图示  
  ![Multiclass Classification]({{page.resource_path}}/multiclass.png)

- - -

课程链接：  

* [Examples and Intuitions I](https://www.coursera.org/learn/machine-learning/lecture/rBZmG/examples-and-intuitions-i)
* [Examples and intuitions II](https://www.coursera.org/learn/machine-learning/lecture/solUx/examples-and-intuitions-ii)
* [Multiclass Classification](https://www.coursera.org/learn/machine-learning/lecture/gFpiW/multiclass-classification)