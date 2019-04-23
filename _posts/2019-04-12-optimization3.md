---
title: "对优化算法进行优化"
categories: ['机器学习']
tags: []
resource_path: /blog/assets/2019/04/12/optimization3
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

{{page.title}}
===

---

学习速率衰减（learning rate decay）
---

代价函数在迭代过程中，会逐步向最小值靠近，但不会完全收敛到极小值点，会在该点附近震荡，这是因为你的学习速率alpha取了固定值以及不同的批次会产生一些噪声。如果你慢慢地降低你的学习速率alpha，那么在初始阶段时由于alpha取值还比较大，学习速率仍然可以比较快。但随着学习速率alpha变小步长也会渐渐变小，素以最终将围绕着离极小值点更近的区域震荡，即使继续训练下去也不会远离。除此之外，它也可以让算法运行的更快。令epochNum为已经遍历数据集的次数，令t为已经进行梯度更新的次数，那么：

$$ \alpha= \frac{1}{1+decayRate*epochNum} $$

当 $$\alpha_0=0.2$$，$$ decayRate=1 $$时：

epoch|$$alpha$$
:-:|:-:
1|0.1
2|0.67
3|0.5
4|0.4
...|...

除此之外，还有一些其它学习速率递减的方法：

* $$ \alpha=0.95^{epochNum} * \alpha_0 $$
* $$ \alpha=\frac{k}{\sqrt{epochNum}} * \alpha_0\ 或\  \frac{k}{\sqrt{t}}*\alpha_0$$

局部最优问题
---

在深度学习的早期阶段，人们常常担心优化算法会陷入到局部最优（Local Optima）之中。但随着深度学习理论的发展，我们对局部最优的理解也在改变。

在一个二维空间、三维空间中，的确很容易有很多局部最优，很多梯度为0的点。然而，我们的特征值常常是很多的。假设我们有两万个特征，即两万个维度，如果一个点要成为局部最优，则需要在所有的两万个方向上的导数都为0，这样的概率是非常小的。

所以，局部最优并不是问题。我们所担心的问题是停滞区（plateaus）。停滞区指的是倒数长时间接近于0的一段区域。在这里你很难找到正确的方向，并且会使学习过程变得非常慢。

---

课程链接：

* [learning rate decay](https://www.coursera.org/learn/deep-neural-network/lecture/hjgIA/learning-rate-decay)
* [The problem of local optima](https://www.coursera.org/learn/deep-neural-network/lecture/RFANA/the-problem-of-local-optima)
