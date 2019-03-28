---
title: 性能度量
categories: ['机器学习']
tags: []
resource_path: /blog/assets/2019/
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

性能度量
===

---

误差分析
---

建模的推荐方法：

1. 以一个简单的算法为开始，这样你可以快速地实现它。实现它并且在你的交叉验证集上测试它。
2. 绘制学习曲线来决定是否需要更多的数据或是更多的特征值等可能会有帮助。
3. **误差分析**：人工研究价差验证集中预测错误的样本，看看是否可以观察到这些样本的类型的任何系统化的趋势（哪种类型的样本错误率更高），找出新的特征值。

误差度量值
---

建议设定某个师叔来评估你的学习算法，并衡量它的表现。比如：在分类问题中，交叉验证集中国样本被正确分类的概率。

错误率与精度
---

这是分类任务中最常用的两种性能度量，既适用于二分类任务，也适用于多分类任务。  
错误率是分类错误的样本数占样本总数的比例。
精度是分类正确的样本数占样本总数的比例。

查准率（precision）与查全率（recall）
---

在癌症分类问题中，精度并非是一个合适的度量标准。我们关注的是“被找出来人的有多少是患有癌症的”和“患有癌症的人中有多少被找出来了”是，就提出了查准率与查全率。  
对于二分类问题，可将样例根据其真实类别与学习器预测类别的组合划分为真正例（true positive）、假正例（false positive）、真反例（true negative）和假反例（false negative）四种情形，令TP、FP、TN、FN 分别表示其对应的样例数。显然 样例总数=TP+FP+TN+FN

.|预测正例|预测反例
:-|:-|:-
**真实正例**|TP(真正例)|FN(假反例)
**真实反例**|FP(假正例)|TN(真反例)

$$
P=\frac{TP}{TP+FP} \\
R=\frac{TP}{TP+FN}
$$

F1度量（F1 score/F score）
---

在分类问题中，如果样本不同分类所占比例的差距过大，比如垃圾邮件分类问题中垃圾邮件占99%，那我们直接把所有邮件都预测为垃圾邮件就可以得到0.99的精度，但显然这并不是合适的。此时我们可以通过F1度量来进行性能度量。
对于癌症分类，有时候我们想要查准率高，以免没病的人接受痛苦的治疗；有时候我们想要查全率高，以免有人因为有癌症而未被查出而离世。我们可以通过改变阈值来实现查准率和查全率的变化。对于多个阈值应该选择哪一个，我们也可以通过F1度量来判断，取最大的一个。  
F1度量是一种综合考虑查准率和查全率的性能度量.是查准率和查全率的调和平均数。

$$
\frac{1}{F1}=\frac{1}{2} \cdot (\frac{1}{P}+\frac{1}{R}) \\
F1=\frac{2 \cdot P \cdot R}{P+R}
$$

$$ F_\beta $$度量
---

F1度量的一般形式——$$F_\beta$$
度量能让表达出对查全率/查准率的偏好。

$$
F_\beta=\frac{(1+\beta^2) \cdot P \cdot R}{(\beta^2 \cdot P)+R} \quad \beta>0
$$

其中
$$\beta$$
度量了查全率对查准率的相对重要性，
$$\beta=1$$
时退化为标准的F1；
$$\beta>1$$
时查全率有更大影响；
$$\beta<1$$
时查准率有更大影响。

混淆矩阵
---

很多时候我们偶多个二分类混淆矩阵，例如：

* 进行多次训练/测试，每次得到一个混淆矩阵
* 在多个数据集上进行训练/测试，希望估计算法的“全局”性能
* 执行多分类任务，每两两类别的组合都对应一个混淆矩阵

总之，我们希望在n个二分类混淆矩阵上综合考察查准率和查全率。我们有两种做法：

1. 现在各混淆矩阵上分别计算出查准率和查全率，再计算平均值，得到“宏查准率”（macro-P）、“宏查全率”（macro-R）以及相应的“宏F1”（macro-F1）：  
   $$
   macro-P = \frac{1}{n} \sum_{i=1}^nP_i \\
   macro-R=\frac{1}{n} \sum_{i=1}^nR_i \\
   macro-F1 = \frac{2 \cdot macro-P \cdot macro-R}{macro-P+macro-R}
   $$
2. 先将各混淆矩阵的对应元素进行平均，得到TP、FP、TN、FN的平均值，分别记为
   $$\bar{TP}、\bar{FP}、\bar{TN}、\bar{Fn}  $$
   ，再基于这些平均值计算出“微查准率”（micro-P）、“微查全率”（micro-R)和“微F1”（micro-F1）：  
   $$
   micro-P = \frac{\bar{TP}}{\bar{TP}+\bar{FP}} \\
   micro-R = \frac{\bar{TP}}{\bar{TP}+\bar{FN}} \\
   micro-F1 = \frac{2 \cdot micro-P \cdot micro-R}{micro-P+micro-R}
   $$

---

课程链接：

* [Error Analysis](https://www.coursera.org/learn/machine-learning/lecture/x62iE/error-analysis)
* [Error Metrics for Skewed Classes](https://www.coursera.org/learn/machine-learning/lecture/tKMWX/error-metrics-for-skewed-classes)
* [Trading Off Precision and Recall](https://www.coursera.org/learn/machine-learning/lecture/CuONQ/trading-off-precision-and-recall)

---

其它参考资料：

* [1]周志华.机器学习[M].北京:清华大学出版社,2016：28-33.