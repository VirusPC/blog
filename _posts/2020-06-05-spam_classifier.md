---
title: SVM应用：垃圾邮件分类
categories: ['机器学习']
tags: [‘spam classifier’]
resource_path: /blog/assets/2020/06/05/spam_classifier
---

<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

垃圾邮件分类
---
1. 预处理邮件（大量邮件）
	1. 转为小写字母
	2. 特殊单词替换：HTML标志，URL，Email地址，
	3. 转化为词干
	4. 移除非单词成分（制表，空格，换行）
2. 单词表
	1. 选择出现频率最高的单词们作为单词表
	2. 为每个单词标记序号
3. 从邮件中提取特征
	1. 构建n维向量，向量下标与单词序号一一对应，存在记为1，不存在记为0.
4. 训练SVM用于垃圾邮件分类
5. 预测


相关资料
---
[相关octave代码以及pdf文件](https://github.com/VirusPC/machine-learning-assignments/tree/master/machine-learning-ex6)



