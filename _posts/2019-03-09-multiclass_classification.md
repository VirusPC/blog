---
title: 多类别分类（Multiclass Classification）
categories: ['机器学习']
tags: []
resource_path: /blog/assets/2019/03/09/multiclass
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

多类别分类
===

---

一对多（One-vs-all）分类算法
---

* 举例：
  * Email标签：工作，朋友，家庭，业余爱好
  * 医学图示：没病，感冒，流感
  * 天气：晴天，多云，雨，雪
* 解决方法：
  有n个类别，就将其拆成n个二分类问题
* 定义  
  为每一类i来训练一个逻辑回归分类器$$h_\theta^{(i)}(x)$$来预测y=i的可能性。  
  对一个新的输入x，比较选择预测概率最大的类i来做出预测结果
  $$ {max}_i(h_\theta^{(i)}(x)) $$

![one-vs-all]({{page.resource_path}}/one-vs-all.png)

$$ h_\theta^{(i)}(x) = P(y=i|x;\theta)\quad(i=1,2,3) $$
- - -
课程链接：  
[Multiclass classification](https://www.coursera.org/learn/machine-learning/lecture/68Pol/multiclass-classification-one-vs-all)
