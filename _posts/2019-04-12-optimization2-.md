---
title: "优化算法：Momentum、RMSprop与Adam"
categories: ['机器学习']
tags: []
resource_path: /blog/assets/2019/04/12/optimization2
---

{% include posts_hook.html %}

{{page.title}}
===

---

指数加权平均（Exponentially weighted averags）
---

指数加权平均在本质上是一种加权求平均的方法，但是比算术平均值要节省内存和计算资源。在算术平均值中，所有元素的权重都为 1/n。而在指数加权平均中，每一个元素的权重是一个指数函数，元素越靠后，其权值越大。

令 $$v_t$$ 为第1天到第t天的平均温度值， $$ \theta_t $$ 为第t天这一天的温度值， $$\beta$$为超参数，我们可以得到它的指数加权平均。$$\beta$$越小，最后一天的温度的权值就越大，平均值受最后一天的影响就越大。如果以t为横坐标，vt的值为纵坐标绘图，那么$$\beta$$越小，我们得到的曲线越平滑。

$$
\begin{align}
v_0&=0\\
v_t&= \beta v_{t-1} + (1-\beta)\theta_t\\
&= \beta^2v_{t-2}+\beta(1-\beta)\theta_{t-1}+(1-\beta)\theta_t\\
&= \qquad ...\\
&=\beta^tv_0+\beta^{t-1}(1-\beta)\theta_1+\beta^{t-2}(1-\beta)\theta_2+\beta^{t-3}(1-\beta)\theta_3+...+(1-\beta)\theta_t\\
&=\beta^{t-1}(1-\beta)\theta_1+\beta^{t-2}(1-\beta)\theta_2+\beta^{t-3}(1-\beta)\theta_3+...+(1-\beta)\theta_t
\end{align}
$$

偏差修正（bias correction)
---
 
在计算 v 的时候，我们会发现 t-vt 图像的前面一部分会非常低，即一开始的指数加权平均值会很小，无法代表平均值。我们可以通过偏差修正来解决这个问题。其具体操作如下：

$$ v_t := \frac{v_t}{1-\beta^t} $$

在学习的初始阶段，偏差修正值可以帮你更好的估计气温值。随着t的增加，偏差修正产生的影响会越来越小。虽然在机器学习中一般不会关心一开始的指数加权平均值，多数的指数加权平均运算并不会使用偏差修正，但是在初始阶段就开始考虑偏差可以帮助你尽早做出更好的估计。

动量梯度下降（Gradient descent with momentum）
---

[上一篇](/blog/机器学习/2019/04/12/optimization_algrithms.html)讲过，与普通的批量梯度下降下降的相比，小批量梯度下降在一次迭代中可以进行多次梯度更新，使得更新速度快于批量梯度下降；但是它每次下降的方向却大多是偏离正确方向的。回想刚刚提到的指数加权平均，它以当前值为主，并且兼顾之前的值，将它应用于小批量梯度下降就会使得每次梯度更新时，不但考虑了本次的数据，还考虑到了之前的数据，使得梯度下降的方向得到了一定程度上的矫正。直观上来说就是通过取平均值，使得代价函数的值的曲线变得更直，更加平滑了。

换个角度，在古典力学里，动量是物体的质量和速度的乘积。它的物理意义指的是：物体在它运动方向上保持运动的趋势。可以把代价函数想象成一个凹凸不平但总体下降的斜坡，动量一方面可以使得小球在到达局部最小值点时再前进一步，有助于跳出局部最小值点。另一方面动量使得小球下降的相对更加偏向于正确的下降的方向。

$$
\begin{align}
&初始化：\\
&\qquad v_{dw}=0,v_{db}=0\\
&在第t次迭代时:\\
&\qquad 计算当前mini-batch上的dw和db\\
&\qquad v_{dw}:=\beta v_{dw}+(1-\beta)dw\\
&\qquad v_{db} := \beta v_{db}+(1-\beta)db\\
&\qquad w := w-\alpha v_{dw}\\
&\qquad b := b-\alpha v_{db}
\end{align}
$$

又因为：

$$
\begin{align}
W:=W-\alpha v_{dw}\\=W-\alpha[\beta v_{dw}+(1-\beta)dW] \\=W-\frac{\alpha}{1-\beta}[(1-\beta)\beta v_{dw}+dW]
\end{align}
$$

令 $$\frac{\alpha}{1-\beta}$$为 新的 $$\alpha$$，我们得到了简化后的更新方式：

$$
\begin{align}
&初始化：\\
&\qquad v_{dw}=0,v_{db}=0\\
&在第t次迭代时:\\
&\qquad 计算当前mini-batch上的dw和db\\
&\qquad v_{dw}:=\beta v_{dw}+dw\\
&\qquad v_{db} := \beta v_{db}+db\\
&\qquad w := w-\alpha v_{dw}\\
&\qquad b := b-\alpha v_{db}
\end{align}
$$

我们一般将$$\beta$$设为0.9

动量梯度下降几乎总会比标准的梯度下降算法更快。它的基本思想是计算梯度的指数加权平均，然后使用它来更新权重。且因为我们只关注最后的值，故无需偏差修正。

均方根反向传播（RMSprop，Root Mean Squared propagation）
---

RMSprop 和 Momentum 一样，都是为了解决代价函数在优化过程中的震荡问题，只不过考虑的角度不同。在权重更新过程中，Momentum 采用了固定的学习速率乘以动态的（兼顾之前的情况）更新值；RMSprop 采用了动态的（兼顾之前的情况）的学习速率乘以当前更新值（梯度）。 而 $$\alpha$$ 损失在下降的过程中可能会在某一方向上出现巨大的震荡。假设只有一个 w 和一个 b，令纵轴代表参数 b，横轴代表参数 w，假设纵轴出现巨大震荡而横轴没有，我们所希望的是减慢垂直方向即 b 方向的学习，并且加速或至少不减慢水平方向的学习（通过修改学习速率），这就是RMSprop算法要做的。

$$
\begin{align}
&初始化：\\
&\qquad s_{dw}=0,s_{db}=0\\
&在第t次迭代时:\\
&\qquad 计算当前mini-batch上的dw和db\\
&\qquad s_{dw}:=\beta s_{dw}+(1-\beta){dw}^2\\
&\qquad s_{db} := \beta s_{db}+(1-\beta){db}^2\\
&\qquad w := w - \frac{\alpha}{\sqrt{s_{dw}+\epsilon}} dw\\
&\qquad b := b - \frac{\alpha}{\sqrt{s_{db}+\epsilon}} db
\end{align}
$$

其中，$$\epsilon$$ 是为了保证分母不太小。一般取$$10^{-8}$$。
![RMSprop]({{page.resource_path}}/RMSprop.png)

让我们结合图像来分析一下这个算法。假设蓝色的轨迹是使用小批量梯度下降算法的优化过程。我们希望的是其在w方向上比b方向上要快。当其在b方向上快时，$$ {db}^2 $$ 就会相对 $$ {dw}^2 $$ 更大，这就使得 $$ s_{db} $$ 变化的要比 $$ s_{dw} $$ 的变化更大，这就使得 $$ \frac{db}{\sqrt{s_{db}}} $$ 变化的要比 $$ \frac{s_{dw}}{\sqrt{s_{dw}}} $$ 变化的更小，也就使得 b 的变化要比 w 的变化要更小。从而达到了防止过大震荡的目的。你可以使用更大的学习速率而不用担心在垂直方向上发散。

比起动量梯度下降，RMSprop进一步优化了代价函数在更新中的更新幅度过大的问题，进一步加快了目标函数的收敛速度。

自适应矩估计优化算法（Adam, Adaptive Moment Estimation）
---

在深度学习发展的过程中，研究者们提出过很多优化算法，但大都只适用于少数问题，并不能被广泛应用在各种不同类型的神经网络上。Adam优化算法是极少数真正可以广泛应用的优化算法之一。

Adam 优化算法本质上是将 Momentum 和 RMSpro p结合起来

$$
\begin{align}
&初始化：\\
&\qquad v_{dw}=0,s_{dw}=0,v_{db}=0,s_{db}=0\\
&在第t次迭代时:\\
&\qquad 计算当前mini-batch上的dw和db\\
&\qquad v_{dw}:=\beta_1 v_{dw}+(1-\beta_1)dw\\
&\qquad v_{db} := \beta_1 v_{db}+(1-\beta_1)db\\
&\qquad s_{dw} := \beta_2 s_{dw}+(1-\beta_2){dw}^2\\
&\qquad s_{db} := \beta_2 s_{db}+(1-\beta_2){db}^2\\
&\qquad v_{dw}^{corrected} = v_{dw}/(1-\beta_1^t)\\
&\qquad v_{db}^{corrected} = v_{db}/(1-\beta_1^t)\\
&\qquad s_{dw}^{corrected} = s_{dw}/(1-\beta_2^t)\\
&\qquad s_{db}^{corrected} = s_{db}/(1-\beta_2^t)\\
&\qquad w :=  w - \frac{\alpha}{\sqrt{s_{dw}^{corrected}+\epsilon}} v_{dw}^{corrected}\\
&\qquad b :=  b - \frac{\alpha}{\sqrt{s_{db}^{corrected}+\epsilon}} v_{db}^{corrected}\\
\end{align}
$$

其中 $$\beta_1$$又被称为一阶矩，$$\beta_2$$又被称为2阶矩 。虽然这个算法有很多超参数，但业内通常对$$\beta_1$$、$$\beta_2$$和$$\epsilon$$都使用默认值。它们并不会影响算法性能，不需要去研究。通常，我们将 $$\beta_1$$ 设为0.9，将$$\beta_2$$设为0.999，将$$\epsilon$$设为$$10^{-8}$$。

将 Adam 优化算法应用在非凸优化问题中所获得的优势：

* 直截了当地实现
* 高效的计算
* 所需内存少
* 梯度对角缩放的不变性（第二部分将给予证明）
* 适合解决含大规模数据和参数的优化问题
* 适用于非稳态（non-stationary）目标
* 适用于解决包含很高噪声或稀疏梯度的问题
* 超参数可以很直观地解释，并且基本上只需极少量的调参

---

课程链接：

* [Exponentially weighted averages](https://www.coursera.org/learn/deep-neural-network/lecture/duStO/exponentially-weighted-averages)
* [Bias correction in exponentially weighted averages](https://www.coursera.org/learn/deep-neural-network/lecture/XjuhD/bias-correction-in-exponentially-weighted-averages)
* [Gradient descent with momentum](https://www.coursera.org/learn/deep-neural-network/lecture/y0m1f/gradient-descent-with-momentum)
* [RMSprop](https://www.coursera.org/learn/deep-neural-network/lecture/BhJlm/rmsprop)
* [Adam optimization algrithm](https://www.coursera.org/learn/deep-neural-network/lecture/w9VCZ/adam-optimization-algorithm)
