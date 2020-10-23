---
title: "神经网络: 反向传播实践"
categories: ['机器学习']
tags: []
resource_path: /blog/assets/2019/03/20/practice
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

反向传播实践
===

---

参数展开
---
* 所用到的函数：  
  ``` matlab
  function [jVAL, gradient] = costFunction(theta)
  ...
  optTheta = fminunc(@costFunction(theta), initialTheta, options)
  ```
  其中，要求```theta```和```initialTheta```都必须为n+1维的向量。
* 在神经网络中（L=4），我们有:  
  $$ \Theta^{(1)},\Theta^{(2)},\Theta^{(3)}-matrices(Theta1, Theta2, Theta3)\\
  D^{(1)},D^{(2)},D^{(3)}-matrices(D1,D2,D3) $$  
  我们需要把他们展开为向量
* 例如：  
  * 变量类型：  
    $$
    s_1=10.\ s_2=10,\ s_3=1\\
    \Theta^{(1)} \in \mathbb{R}^{10 \times 11},\ \Theta^{(2)} \in \mathbb{R}^{(10 \times 11)},\ \Theta^{(3)} \in \mathbb{1 \times 11}\\
    D^{(1)} \in \mathbb{R}^{10 \times 11},\ D^{(2)} \in \mathbb{R}^{(10 \times 11)}, D6{(3)} \in \mathbb{R}^{1 \times 11}
    $$
  * 向量展开
    ```matlab
    thetaVec = [Theta1(:); Theta2(:); Theta3(:)];  % 列优先展开
    DVec = [D1(:); D2(:); D3(:)]
    ```
  * 逆向取出：  
    ```matlab
    Theta1 = reshape(thetaVec(1:110), 10, 11);
    Theta2 = reshape(thetaec(111:220), 10, 11);
    Theta3 - reshape(thetaVec(221:231), 1, 11)
    ```

梯度检查（Gradient Checking）
---

* 反向传播有很多复杂容易出错的细节，我们可以通过梯度检查来减小错误的概率。
* 梯度的数字估计：  
  ![numerical estimation]({{page.resource_path}}/estimation.png)  
  two-sided difference（更准确）
  $$ \frac{d}{d \theta}J(\theta) \approx \frac{J(\theta + \epsilon)-J(\theta - \epsilon)}{2\epsilon}$$  
  或 one-sided difference
  $$ \frac{d}{d \theta}J(\theta) \approx \frac{J(\theta + \epsilon)-J(\theta)}{\epsilon}$$  
  一般，我们取
  $$\epsilon=10^{-4}$$
* 当有多个参数时按多元函数微分计算：  
  $$
  \theta \in \mathbb{R}^n \quad (E.g.\ \theta 是\Theta^{(1)},\Theta^{(2)},\Theta^{(3)}的展开版本)\\
  \theta = [\theta_1, \theta_2,\theta_3,...\theta_n]^T\\
  \frac{\partial}{\partial \theta_1}J(\theta) \approx \frac{J(\theta_1+\epsilon,\theta_2,\theta_3,...,\theta_n) - J(\theta_1-\epsilon, \theta_2,\theta_3,...,\theta_n)}{2\epsilon}\\
  \frac{\partial}{\partial\theta_1}J(\theta) \approx \frac{J(\theta_1,\theta_2+\epsilon,\theta_3,...,\theta_n) - J(\theta_1,\theta_2-\epsilon,\theta_3,...,\theta_n)}{2\epsilon}\\
  ...
  $$
*  代码实现：
  ```matlab
  for i = 1:n,
      thetaPlus = theta;
      thetaPlus(i) = thetaPlus(i) + EPSILON;
      thetaMinus = theta;
      thetaMinus(i) = thetaMinus(i) - EPSILON;
      gradApprox(i) = (J(thetaPlus) - J(thetaMinus))/(2*EPSILON);
  end;
  ```  
  检查$$gradApprox \approx DVec$$
* 注意：在开始学习之前，一定要关掉我们的梯度检验。  
  原因是这个计算过程，实际上代价更高，复杂度也很高，这不是一个很好的计算导数的方法。相反，我们前面讨论的反向传播算法相比之下就非常高效。所以，一旦检验证明你的算法没有错误，就要把梯度检验关掉。

随机初始参数
---

* 如果我们仍旧像之前那样，把所有的参数值都设为0或者其他一样的数值，会导致同一层的所有的结点都重复更新为相同的值。所以我们需要随机的初始化我们的参数矩阵。这种方法被称为 Symmetry breaking
* Symmetry breaking就是把每个
$$\Theta_{ij}^{(l)}$$
初始化为
$$\ [-\delta, \delta] \ $$
内的一个随机值。
* 代码实例：  
  ```matlab
  % 假设Theta1的维度是10*11，Theta2的维度是1*11
  Theta1 = rand(10,11)*(2*INIT_EPSILON)-INIT_EPSILON;
  Theta2 = rand(1,11)*(2*INIT_EPSILON)-INIT_EPSILON;
  ```
* 其中，rand函数是生成一个随机数矩阵，范围在0~1之间

---
课程链接：  
* [Implementation Noete: Unrolling Parameters](https://www.coursera.org/learn/machine-learning/supplement/v88ik/implementation-note-unrolling-parameters)
* [Gradient Checking](https://www.coursera.org/learn/machine-learning/supplement/fqeMw/gradient-checking)
* [Random Initialization](https://www.coursera.org/learn/machine-learning/supplement/KMzY7/random-initialization)
* [Putting It Together](https://www.coursera.org/learn/machine-learning/supplement/Uskwd/putting-it-together)