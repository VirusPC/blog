---
title: putting it together
categories: ['机器学习']
tags: []
resource_path: /blog/assets/2019/
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

建立神经网络的步骤
===

---

如何选择神经网络的结构？
---

选择你的神经网络的布局，包括每一层有多少隐含单元和你一共需要多少层。

* 输入单元的数量 = 特征的维度
* 输出单元的数量 = 类的数量
* 每层隐含层的隐含单元的数量 = 通常越多越好（必须与消耗的计算资源相平衡，越多隐含单元需要消耗月u都的计算资源）
* 默认：一层隐含层。如果你有多于一个的隐含层，那么建议你所有的隐含层拥有同样数量的隐含单元。

训练神经网络
---

1. 随机初始化权重
2. 实现正向传播，来为每个$$x^{(i)}$$获取预测值$$h_\Theta(x^{(i)})$$
3. 实现损失函数
4. 实现后向传播来计算偏导
5. 使用梯度检查来确认你的后向传播是有效的。然后**关闭**它。
6. 使用梯度小将或内置的优化函数通过改变theta的权重来最小化损失函数。


- - -
课程链接：  
[Putting it Together](https://www.coursera.org/learn/machine-learning/supplement/Uskwd/putting-it-together)