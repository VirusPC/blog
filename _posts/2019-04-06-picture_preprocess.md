---
title: 图像预处理
categories: ['图像处理']
tags: [‘opencv’]
resource_path: /blog/assets/2019/04/06
---

{% include posts_hook.html %}

图像预处理
===

---

一张图片可以分为目标，背景和噪声。在进行机器学习之前，我们需要对图像进行预处理。

基本流程：

1. 灰度化
2. 二值化
3. 降噪
4. 字符倾斜度调整
5. 字符分割
6. 归一化

灰度化
---

彩色图像一个像素点对应RGB即红绿蓝三个值，通过改变它们的值可以组合出其它的所有颜色。而灰度图一个像素点只对应一个Gray即灰度值用来表示亮度。将RGB图的值都设为Gray也是灰度图。它们的范围都在0~255之间。

将彩色图像转换为灰度图，有很多种方法：

* 分量法。用RGB三个分量其中的一个分量作为灰度值：  
  Gray=B 或 Gray=G 或 Gray=R
* 最大值法。用RGB三个分量中最大的一个作为灰度值：  
  Gray = max(R, G, B)
* 平均值法。取RGB三个分量的平均值作为灰度值。  
  Gray = (R+G+B)/3
* 加权平均法。
  * 由于人眼对绿色比较敏感，而对蓝色不那么敏感，因此可按照下式设置权重：  
    Gray = 0.30*R + 0.59*G + 0.11*B
  * opencv所采用的一种权重：  
    Gray = 0.11*B + 0.59*G+0.30*R

二值化
---

 图像二值化的目的是最大限度的将图象中感兴趣的部分保留下来。二值化就是将灰度图的像素点的值全部设为0或255，使图片呈现出明显的黑白效果。我们可以设置一个阈值，使得灰度值大于阈值的都设为255，小于阈值的都设为0。

常见的二值化方法可以分为固定阈值和自适应阈值。固定阈值即对所有的图片都采用同一阈值，但当背景和目标像素都接近于0或都接近于255时，效果就不会那么理想。自适应阈值会根据当前图片的像素自动调整阈值，比如我们可以选取像素的平均值作为阈值。

降噪
---

在二值化过程中，一些数字之外的东西也被二值化为图像的前景部分，这会给后续预处理步骤造成很多困难，所以我们需要想办法去除噪声。