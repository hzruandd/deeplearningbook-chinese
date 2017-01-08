---
title: 线性因子模型
layout: post
share: false
---




许多深度学习的研究前沿涉及到了构建输入的概率模型$p_{\text{model}}(\Vx)$。
原则上说，给定任何其他变量的情况下，这样的模型可以使用概率推断来预测其环境中的任何变量。
许多这样的模型还具有隐变量$\Vh$，其中$p_{\text{model}}(\Vx) = \SetE_{\Vh} p_{\text{model}}(\Vx\mid\Vh)$。
这些隐变量提供了表示数据的另一种方式。
 基于隐变量的分布式表示可以有很多优点，这些我们在深度前馈网络和循环神经网络中已经发现。
<!-- % 479 -->


在本章中，我们描述了一些带有隐变量的最简单的概率模型：线性因子模型。
这些模型有时被用来构建混合块模型{cite?}或者更大的深度概率模型{cite?}。
他们还展示了构建生成模型所需的许多基本方法，更先进的深层模型也将在此基础上进一步扩展。
<!-- % 479 -->


线性因子模型通过使用随机线性解码器函数来定义，该函数通过对$\Vh$的线性变换以及添加噪声来生成$\Vx$。
<!-- % 479 -->


这些模型很有趣，因为它们使得我们能够发现一些拥有简单联合分布的解释性因子。 
线性解码器的简单性使得这些模型（含有隐变量的模型）能够被广泛研究。
<!-- % 479 -->


线性因子模型描述如下的数据生成过程。 
首先，我们从一个分布中抽取解释性因子
\begin{align}
\RVh \sim p(\Vh),
\end{align}
其中$p(\Vh)$是一个因子的分布，满足$p(\Vh) = \prod_{i}^{}p(h_i)$，所以很容易从中采样。
接下来，在给定因子的情况下，我们对实值的可观察变量进行抽样
\begin{align}
\Vx = \MW \Vh + \Vb + \text{noise},
\end{align}
其中噪声通常是对角化的（在维度上是独立的）且服从高斯分布。
在\fig?有具体说明。
<!-- % 480 -->

\begin{figure}[!htb]
\ifOpenSource
\centerline{\includegraphics{figure.pdf}}
\else
	\centerline{\includegraphics{Chapter13/figures/linear_factors}}
\fi
	\caption{描述线性因子模型族的有向图模型，其中我们假设一个观察到的数据向量$\Vx$是通过独立的隐含因子$\Vh$的线性组合获得的，加上一定的噪音。
		不同的模型，比如概率PCA，因子分析或者是ICA，都是选择了不同形式的噪音以及先验$p(\Vh)$。}
\end{figure}



# 概率PCA和因子分析

<!-- % 480 -->

概率PCA，因子分析和其他线性因子模型是上述等式（\eqn?,\eqn?）的特殊情况，并且仅在对观测到$\Vx$之前的噪声分布和隐变量$\Vh$的先验的选择上不同。
<!-- % 480 -->

因子分析{cite?}中，隐变量的先验是一个方差为单位矩阵的高斯分布
\begin{align}
\RVh \sim \CalN(\Vh; \mathbf{0},\MI),
\end{align}
同时，假定观察值$x_i$在给定$\Vh$的条件下是条件独立的。
具体的说，噪声可以被假设为是从对角的协方差矩阵的高斯分布中抽出的，协方差矩阵为$\Vpsi = \text{diag}(\Vsigma^2)$，其中$\Vsigma^2 = [\sigma_1^2,\sigma_2^2,\ldots,\sigma_n^2]^{\top}$表示一个向量。
<!-- % 480 -->


因此，隐变量的作用是捕获不同观测变量$x_i$之间的依赖关系。
实际上，可以容易地看出$\Vx$是多变量正态分布，并满足
\begin{align}
\RVx \sim \CalN(\Vx; \Vb, \MW\MW^{\top}+\Vpsi).
\end{align}
<!-- % 480 end -->



<!-- % 481 head -->
为了将PCA引入到概率框架中，我们可以对因子分析模型进行轻微修改，使条件方差$\sigma_i^2$等于同一个值。
在这种情况下，$\Vx$的协方差是$\MW\MW^{\top}+\sigma^2\MI$，这里的$\sigma^2$是一个标量。
由此可以得到条件分布，如下：
\begin{align}
\RVx \sim \CalN(\Vx; \Vb, \MW\MW^{\top} + \sigma^2\MI ),
\end{align}
或者等价于
\begin{align}
\RVx = \MW\RVh + \Vb + \sigma\RVz,
\end{align}
其中$\RVz \sim \CalN(\Vz;\mathbf{0},\MI)$是高斯噪音。
之后{tipping99mixtures}提出了一种迭代的EM算法来估计参数$\MW$和$\sigma^2$。
<!-- % 481 -->


概率PCA模型利用了这样一种观察的现象：除了一些小的重构误差$\sigma^2$，数据中的大多数变化可以由隐变量$\Vh$描述。
通过{tipping99mixtures}的研究可以发现，当$\sigma \xrightarrow{} 0$的时候，概率PCA等价于PCA。
在这种情况下，给定$\Vx$情况下的$\Vh$的条件期望等于将$\Vx - \Vb$投影到$\MW$的$d$列的生成空间，与PCA一样。
<!-- % 481 -->

当$\sigma\xrightarrow{} 0$的时候，概率PCA所定义的密度函数在$\MW$的$d$维列生成空间的坐标周围非常尖锐。
如果数据实际上没有集中在超平面附近，这会导致模型为数据分配非常低的可能性。
<!-- % 481 -->


# 独立分量分析

<!-- % 481 -->


独立分量分析是最古老的表示学习算法之一{cite?}。
它是一种建模线性因子的方法，旨在分离观察到的信号，并转换为许多基础信号的叠加。
这些信号是完全独立的，而不是仅仅彼此不相关\footnote{\sec?讨论了不相关变量和独立变量之间的差异。}。
<!-- % 481 -->


许多不同的具体方法被称为独立分量分析。
与我们本书中描述的其他生成模型最相似的独立分量分析变种是训练完全参数化的生成模型{cite?}。
隐含因子$\Vh$的先验$p(\Vh)$，必须由用户给出并固定。
接着模型确定性地生成$\Vx = \MW \Vh$。
我们可以通过非线性变化（使用\eqn?）来确定$p(\Vx)$。
然后通过一般的方法比如最大似然估计进行学习。
<!-- % 482 head -->


这种方法的动机是，通过选择一个独立的$p(\Vh)$，我们可以尽可能恢复接近独立的隐含因子。
这是一种常用的方法，它并不是用来捕捉高级别的抽象的因果因子，而是恢复已经混合在一起的低级别信号。
在该设置中，每个训练样本对应一个时刻，每个$x_i$是一个传感器的对混合信号的观察值，并且每个$h_i$是单个原始信号的一个估计。
例如，我们可能有$n$个人同时说话。 
如果我们具有放置在不同位置的$n$个不同的麦克风，则独立分量分析可以检测每个麦克风的音量变化，并且分离信号，使得每个$h_i$仅包含一个人清楚地说话。
这通常用于脑电图的神经科学，一种用于记录源自大脑的电信号的技术。
放置在对象的头部上的许多电极传感器用于测量来自身体的许多电信号。
实验者通常仅对来自大脑的信号感兴趣，但是来自受试者的心脏和眼睛的信号强到足以混淆在受试者的头皮处进行的测量。
信号到达电极，并且混合在一起，因此独立分量分析是必要的，以分离源于心脏与源于大脑的信号，并且将不同脑区域中的信号彼此分离。
<!-- % 482 -->


如前所述，独立分量分析存在许多变种。
一些版本在$\Vx$的生成中添加一些噪声，而不是使用确定性的解码器。
大多数不使用最大似然估计准则，而是旨在使$\Vh = \MW^{-1}\Vx$的元素彼此独立。
许多准则能够达成这个目标。
\eqn?需要用到$\MW$的行列式，这可能是昂贵且数值不稳定的操作。
独立分量分析的一些变种通过将$\MW$约束为正交来避免这个有问题的操作。
<!-- % 482 -->


独立分量分析的所有变种要求$p(\Vh)$是非高斯的。
这是因为如果$p(\Vh)$是具有高斯分量的独立先验，则$\MW$是不可识别的。
对于许多$\MW$值，我们可以在$p(\Vx)$上获得相同的分布。 
这与其他线性因子模型有很大的区别，例如概率PCA和因子分析，通常要求$p(\Vh)$是高斯的，以便使模型上的许多操作具有闭式解。
在用户明确指定分布的最大似然估计方法中，一个典型的选择是使用$p(h_i) = \frac{d}{dh_i}\sigma(h_i)$。
这些非高斯分布的典型选择在$0$附近具有比高斯分布更高的峰值，因此我们也可以看到独立分量分析经常在学习稀疏特征时使用。
<!-- % 483 head -->




按照我们的说法独立分量分析的许多变种不是生成模型。
 在本书中，生成模型可以直接表示$p(\Vx)$，也可以认为是从$p(\Vx)$中抽取样本。
独立分量分析的许多变种仅知道如何在$\Vx$和$\Vh$之间变换，但没有任何表示$p(\Vh)$的方式，因此也无法确定$p(\Vx)$。
例如，许多独立分量分析变量旨在增加$\Vh = \MW^{-1}\Vx$的样本峰度，因为高峰度使得$p(\Vh)$是非高斯的，但这是在没有显式表示$p(\Vh)$的情况下完成的。
这是为什么独立分量分析被常用作分离信号的分析工具，而不是用于生成数据或估计其密度。
<!-- % 483 head -->


正如PCA可以推广到\chap?中描述的非线性自动编码器，独立分量分析可以推广到非线性生成模型，其中我们使用非线性函数$f$来生成观测数据。
关于非线性独立分量分析最初的工作可以参考{hyvarinen1999nonlinear}，它和集成学习的成功结合可以参见{roberts2001independent,lappalainen2000nonlinear}。
独立分量分析的另一个非线性扩展是非线性独立分量估计方法{cite?}，这个方法堆叠了一系列可逆变换（编码器），从而能够高效地计算每个变换的Jacobian行列式。
这使得我们能够精确地计算似然，并且像ICA一样，NICE尝试将数据变换到具有可分解的边缘分布的空间。
由于非线性编码器的使用\footnote{译者注：相比于ICA}，这种方法更可能成功。
因为编码器和一个与其（编码器）完美逆作用的解码器相关联，所以可以直接从模型生成样本（通过首先从$p(\Vh)$采样，然后应用解码器）。
<!-- % 483 -->


独立分量分析的另一个应用是通过在组内鼓励统计依赖关系在组之间抑制依赖关系来学习一组特征。
当相关单元的组不重叠时，这被称为独立子空间分析。
还可以向每个隐藏单元分配空间坐标，并且空间上相邻的单元形成一定程度的重叠。
这能够鼓励相邻的单元学习类似的特征。
当应用于自然图像时，这种拓扑独立分量分析方法学习Gabor滤波器，从而使得相邻特征具有相似的定向，位置或频率。
在每个区域内出现类似Gabor函数的许多不同相位偏移，使得在小区域上的合并产生了平移不变性。
<!-- % 483 end -->



# 慢特征分析

<!-- % 484 head -->


慢特征分析是使用来自时间信号的信息来学习不变特征的线性因子模型{cite?}。
<!-- % 484 -->


SFA的想法源于所谓的慢原则。
其基本思想是，与场景中的描述作用的物体相比，场景的重要特性通常变化得非常缓慢。
例如，在计算机视觉中，单个像素值可以非常快速地改变。
如果斑马从左到右移动穿过图像并且它的条纹穿过对应的像素时，该像素将迅速从黑色变为白色，并再次恢复。
通过比较，指示斑马是否在图像中的特征将根本不改变，并且描述斑马的位置的特征将缓慢地改变。
因此，我们可能希望规范我们的模型，从而能够学习到随时间变化缓慢的特征。
<!-- % 484 -->


慢原则早于SFA，并已被应用于各种模型{cite?}。
一般来说，我们可以将慢原则应用于可以使用梯度下降训练的任何可微分模型。 
为了引入慢原则，我们可以通过向代价函数添加以下项
\begin{align}
\lambda \sum_t L(f(\Vx^{(t+1)}),f(\Vx^{(t)})),
\end{align}
其中$\lambda$是确定慢度正则化的强度的超参数项，$t$是样本时间序列的索引，$f$是特征提取器，$L$是测量$f(\Vx^{(t)})$和$f(\Vx^{(t+1)})$之间的距离的损失函数。
$L$的一个常见选择是平均误差平方。
<!-- % 484 -->


SFA是慢原则中特别有效的应用。
由于它被应用于线性特征提取器，并且可以通过闭式解训练，所以它是高效的。
像ICA的一些变体一样，SFA本身不是生成模型，只是在输入空间和特征空间之间定义了线性映射，但是没有定义特征空间的先验，因此输入空间中不存在$p(\Vx)$分布。
<!-- % 484 -->


SFA算法{cite?}包括将$f(\Vx;\theta)$定义为线性变换，并求解满足如下约束
\begin{align}
\SetE_t  f(\Vx^{(t)})_i = 0 
\end{align}
以及
\begin{align}
\SetE_t [ f(\Vx^{(t)})_i^2 ] =1 
\end{align}
的优化问题
\begin{align}
\min_{\Vtheta} \SetE_t  (f(\Vx^{(t+1)})_i - f(\Vx^{(t)})_i  )^2.
\end{align}

<!-- % 485 -->

学习特征具有零均值的约束对于使问题具有唯一解是必要的; 否则我们可以向所有特征值添加一个常数，
并获得具有慢度目标的相等值的不同解。
特征具有单位方差的约束对于防止所有特征趋近于$0$的病态问题是必要的。
与PCA类似，SFA特征是有序的，其中学习第一特征是最慢的。
要学习多个特征，我们还必须添加约束
\begin{align}
\forall i<j,\ \  \SetE_t [f(\Vx^{(t)})_i  f(\Vx^{(t)})_j] = 0.
\end{align}
这要求学习的特征必须彼此线性去相关。 
没有这个约束，所有学习的特征将简单地捕获一个最慢的信号。
可以想象使用其他机制，如最小化重构误差，迫使特征多样化。
但是由于SFA特征的线性，这种去相关机制只能得到一种简单的解。 
SFA问题可以通过线性代数软件获得闭式解。
<!-- % 485 -->



在运行SFA之前，SFA通常通过对$\Vx$使用非线性的基扩充来学习非线性特征。
例如，通常用$\Vx$的二次基扩充来代替原来的$\Vx$，得到一个包含所有$x_ix_j$的向量。
然后可以通过重复学习线性SFA特征提取器，对其输出应用非线性基扩展，然后在该扩展之上学习另一个线性SFA特征提取器，来组合线性SFA模块以学习深非线性慢特征提取器。
Linear SFA modules may then be composed to learn deep nonlinear slow feature extractors by repeatedly learning a linear SFA feature extractor, applying a nonlinear basis expansion to its output, and then learning another linear SFA feature extractor on top of that expansion.
<!-- % 485 -->


当训练在自然场景的视频的小空间补丁的时候，使用二次基扩展的SFA能够学习到与V1皮层中那些复杂细胞类似的许多特征{cite?}。
当训练在3-D计算机呈现环境内的随机运动的视频时，深度SFA模型能够学习到与大鼠脑中用于导航的神经元学到的类似的特征{cite?}。
因此从生物学角度上说SFA是一个合理的有依据的模型。
<!-- % 485 -->



SFA的一个主要优点是，即使在深度非线性条件下，它依然能够在理论上预测SFA能够学习哪些特征。
为了做出这样的理论预测，必须知道关于配置空间的环境的动态（例如，在3D渲染环境中的随机运动的情况下，理论分析出位置，相机的速度的概率分布）。
已知潜在因子如何改变的情况下，我们能够理论分析解决表达这些因子的最佳函数。
在实践中，基于模拟数据的实验上，使用深度SFA似乎能够恢复了理论预测的函数。
相比之下其他学习算法中的代价函数高度依赖于特定像素值，使得更难以确定模型将学习什么特征。
<!-- % 486 -->


深度SFA也已经被用于学习用在对象识别和姿态估计的特征{cite?}。
到目前为止，慢原则尚未成为任何最先进的技术应用的基础。
究竟是什么因素限制了其性能也有待研究。
我们推测，或许慢度先验是太过强势，并且，最好添加这样一个先验使得当前步骤到下一步的预测更加容易，而不是加一个先验使得特征应该近似为一个常数。
对象的位置是一个有用的特征，无论对象的速度是高还是低。 但慢原则鼓励模型忽略具有高速度的对象的位置。
<!-- % 486 -->



# 稀疏编码

<!-- % 486 -->


稀疏编码{cite?}是一个线性因子模型，已作为无监督特征学习和特征提取机制进行了大量研究。
严格地说，术语"稀疏编码"是指在该模型中推断$\Vh$的值的过程，而"稀疏建模"是指设计和学习模型的过程，但是通常这两个概念都可以用术语"稀疏编码"描述。
<!-- % 486 -->

像其它的线性因子模型一样，它使用了线性的解码器加上噪音的方式获得一个$\Vx$的重构，就像\eqn?描述的一样。
更具体的说，稀疏编码模型通常假设线性因子有一个各向同性的精度为$\beta$的高斯噪音：
\begin{align}
p(\Vx\mid \Vh) = \CalN
(\Vx;\MW\Vh + \Vb ,\frac{1}{\beta}\MI).
\end{align}
<!-- % 486 -->


关于$p(\Vh)$分布通常选择一个峰值很尖锐且接近$0$的分布{cite?}。
常见的选择包括了可分解的Laplace，Cauchy或者可分解的Student-t分布。
例如，以稀疏惩罚系数$\lambda$为参数的Laplace先验可以表示为
\begin{align}
p(h_i) = \text{Laplace}(h_i;0,\frac{2}{\lambda}) = \frac{\lambda}{4} \text{e}^{ -\frac{1}{2}\lambda \vert h_i\vert},
\end{align}
相应的，Student-t分布可以表示为
\begin{align}
p(h_i)\propto \frac{1}{(1+\frac{h_i^2}{\nu})^{\frac{\nu+1}{2}}}.
\end{align}
<!-- % 487 head -->

使用最大似然估计的方法来训练稀疏编码模型是不可行的。
相反，为了在给定编码的情况下更好地重建数据，训练过程在编码数据和训练解码器之间交替进行。
稍后在\sec?中，这种方法将被进一步证明为解决似然最大化问题的一种通用的近似方法。
<!-- % 487 -->

对于诸如PCA的模型，我们已经看到使用了预测$\Vh$的参数化的编码器函数，并且该函数仅包括乘以权重矩阵。
在稀疏编码中的编码器不是参数化的。
相反，编码器是一个优化算法，在这个优化问题中，我们寻找单个最可能的编码值：
\begin{align}
\Vh^* = f(\Vx) = \underset{\Vh}{\arg\max}\  p(\Vh\mid\Vx).
\end{align}
<!-- % 487 -->


结合\eqn?和\eqn?，我们得到如下的优化问题：
\begin{align}
& \underset{\Vh}{\arg\max}\  p(\Vh\mid\Vx) \\
= & \underset{\Vh}{\arg\max}\ \log  p(\Vh\mid\Vx)\\
= & \underset{\Vh}{\arg\min}\ \lambda \Vert \Vh\Vert_1 + \beta  \Vert \Vx - \MW \Vh\Vert_2^2,
\end{align}
其中，我们扔掉了与$\Vh$无关的项，除以一个正的伸缩因子来简化表达。
<!-- % 487 -->

由于在$\Vh$上施加$L^1$范数，这个过程将产生稀疏的$\Vh^*$（\sec?）。
<!-- % 487 -->

为了训练模型而不仅仅是进行推断，我们交替迭代关于$\Vh$和$\MW$的最小化过程。
在本文中，我们将$\beta$视为超参数。
通常将其设置为$1$，因为其在此优化问题中的作用与$\lambda$类似，没有必要使用两个超参数。 
原则上，我们还可以将$\beta$作为模型的参数，并学习它。
我们在这里已经放弃了一些不依赖于$\Vh$但依赖于$\beta$的项。
要学习$\beta$，必须包含这些项，否则$\beta$将退化为$0$。
<!-- % 487 -->


不是所有的稀疏编码方法都显式地构建了$p(\Vh)$和$p(\Vx\mid\Vh)$。 
通常我们只是对学习一个带有激活值的特征的字典感兴趣，当使用这个推断过程时，这个激活值通常为$0$。
<!-- % 487 end -->

如果我们从Laplace先验中采样$\Vh$，$\Vh$的元素实际上为零是一个零概率事件。
生成模型本身并不稀疏，只有特征提取器是。
{Goodfeli-et-al-TPAMI-Deep-PrePrint-2013-small}描述了不同模型族中的近似推断，和尖峰和平板稀疏编码模型，其中先验的样本通常包含许多0。
<!-- % 488 head -->

与非参数化编码器结合的稀疏编码方法原则上可以比任何特定的参数化编码器更好地最小化重构误差和对数先验的组合。
另一个优点是编码器没有泛化误差。
参数化的编码器必须泛化地学习如何将$\Vx$映射到$\Vh$。
对于与训练数据差异很大的异常的$\Vx$，所学习的参数化的编码器可能无法找到对应精确重建的$\Vh$或稀疏的编码。
对于稀疏编码模型的绝大多数形式，推断问题是凸的，优化过程将总是找到最优值（除非出现简并的情况，例如重复的权重向量）。
显然，稀疏和重构成本仍然可以在不熟悉的点上升，但这归因于解码器权重中的泛化误差，而不是编码器中的泛化误差。
当稀疏编码用作分类器的特征提取器时，而不是使用参数化的函数来预测时，基于优化的稀疏编码模型的编码过程中泛化误差的减小可导致更好的泛化能力。
{Coates2011b}证明了在对象识别任务中稀疏编码特征比基于参数化的编码器（如线性sigmoid自动编码器）的特征拥有更好的泛化能力。
受他们的工作启发，{Goodfeli-et-al-TPAMI-Deep-PrePrint-2013-small}表明稀疏编码的变体在其中极少标签（每类20个或更少标签）的情况中比其他特征提取器拥有更好的泛化能力。
<!-- % 488  -->



非参数编码器的主要缺点是在给定$\Vx$的情况下需要大量的时间来计算$\Vh$，因为非参数方法需要运行迭代算法。
在\chap?中讲到的参数化的自动编码器方法仅使用固定数量的层，通常只有一层。
另一个缺点是它不直接通过非参数编码器进行反向传播，这使得我们很难采用先使用无监督方式预训练稀疏编码模型然后使用监督方式对其进行微调的方法。
允许近似导数的稀疏编码模型的修改版本确实存在但未被广泛使用{cite?}。
<!-- % 488  end -->

像其他线性因子模型一样，稀疏编码经常产生糟糕的样本，如\fig?所示。
即使当模型能够很好地重构数据并为分类器提供有用的特征时，也会发生这种情况。
这种现象原因是每个单独的特征可以很好地被学习到，但是隐含结点因子的先验会导致模型包括每个生成的样本中的所有特征的随机子集。
这促使人们在深度模型中的最深层以及一些复杂成熟的浅层模型上施加一个非因子的分布。

\begin{figure}[!htb]
\ifOpenSource
\centerline{\includegraphics{figure.pdf}}
\else
	\centerline{\includegraphics{Chapter13/figures/s3c_samples}}
\fi
\caption{尖峰和平板稀疏编码模型上在MNIST数据集训练的样例和权重。
	（左）这个模型中的样本和训练样本相差很大。
	第一眼看来，我们可以认为模型拟合得很差。
	（右）这个模型的权重向量已经学习到了如何表示笔迹，有时候还能写完整的数字。
	因此这个模型也学习到了有用的特征。
	问题在于特征的因子的先验会导致特征子集合随机的组合。
	一些这样的子集能够合成可识别的MNIST集上的数字。
	这也促进了拥有更强大的隐含编码的生成模型的发展。
	此图是从{Goodfeli-et-al-TPAMI-Deep-PrePrint-2013-small}中拷贝来的，并获得允许。}
\end{figure}

这促进了更深层模型的发展，可以在最深层上施加non-factorial分布，以及开发更复杂的浅层模型。
<!-- % 489 head -->



# PCA的流形解释

<!-- % 489 au -->


线性因子模型，包括了PCA和因子分析，可以理解为学习一个流形{cite?}。
我们可以将概率PCA定义为高概率的薄饼状区域，即高斯分布，沿着某些轴非常窄，就像薄饼沿着其垂直轴非常平坦，但沿着其他轴是细长的，正如薄饼在其水平轴方向是很宽的一样。
\fig?解释了这种现象。
PCA可以理解为将该薄饼与更高维空间中的线性流形对准。
这种解释不仅适用于传统PCA，而且适用于学习矩阵$\MW$和$\MV$的任何线性自动编码器，其目的是使重构的$\Vx$尽可能接近于原始的$\Vx$。
<!-- % 489 end -->

\begin{figure}[!htb]
\ifOpenSource
\centerline{\includegraphics{figure.pdf}}
\else
	\centerline{\includegraphics{Chapter13/figures/PPCA_pancake_color}}
\fi
	\caption{平坦的高斯能够描述一个低维流形附近的概率密度。
		此图表示了"流形平面"上的"馅饼"的上半部分并且穿过了它的中心。
		正交于流形方向（指出平面的箭头）的方差非常小，可以被视作是"噪音"，其它方向（平面内的箭头）的方差则很大，对应了"信号"以及低维数据的坐标系统。}
\end{figure}


编码器表示为
\begin{align}
\Vh  = f(\Vx) = \MW^{\top} (\Vx - \Vmu).
\end{align}
<!-- % 490 head -->


编码器计算$h$的低维表示。
从自动编码器的角度来看，解码器负责重构计算
\begin{align}
\hat{\Vx} = g(\Vh) = \Vb + \MV \Vh.
\end{align}
<!-- % 490 -->


能够最小化重构误差
\begin{align}
\SetE[\Vert\Vx - \hat{\Vx}\Vert^2]
\end{align}
的线性编码器和解码器的选择对应着$\MV = \MW$，${\Vmu} = \Vb = \SetE[\Vx]$， $\MW$的列 形成一组正交基，这组基生成的子空间相同于主特征向量 对应的协方差矩阵$\MC$
\begin{align}
\MC = \SetE[(\Vx - {\Vmu})(\Vx - {\Vmu})^{\top}].
\end{align}
<!-- % 490 end -->



在PCA中，$\MW$的列是按照对应的特征值（其全部是实数和非负数）的大小排序所对应的特征向量。
<!-- % 490 end -->

我们还可以发现$\MC$的特征值$\lambda_i$对应了$\Vx$在特征向量$\Vv^{(i)}$方向上的方差。
如果$\Vx\in \SetR^D$，$\Vh\in\SetR^d$并且满足$d<D$，则（给定上述的${\Vmu},\Vb,\MV,\MW$的情况下）最佳的重构误差是
\begin{align}
\min \SetE[\Vert \Vx - \hat{\Vx} \Vert^2] = \sum_{i=d+1}^{D}\lambda_i.
\end{align}
因此，如果协方差矩阵的秩为$d$，则特征值$\lambda_{d+1}$到$\lambda_{D}$都为$0$，并且重构误差为$0$。
<!-- % 491 head  -->

此外，还可以证明上述解可以通过在正交矩阵$\MW$下最大化$\Vh$元素的方差而不是最小化重构误差来获得。
<!-- % 491  -->


某种程度上说，线性因子模型是最简单的生成模型和学习数据表示的最简单模型。
许多模型比如线性分类器和线性回归模型可以扩展到深度前馈网络，这些线性因子模型可以扩展到执行的是相同任务但具有更强大和更灵活的模型族，比如自动编码器网络和深概率模型。
<!-- % 491    -->














