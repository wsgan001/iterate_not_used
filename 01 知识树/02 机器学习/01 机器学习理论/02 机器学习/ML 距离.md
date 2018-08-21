
# ML 距离


TODO

* **深入细致的总结下，距离中涉及到的公式推导及理论源头都要追究到，该扩展就要扩展，甚至每个距离写一篇都可以，然后再写一篇总的对比和场景说明。**
* **要把每个距离使用在什么场景下，为什么适用，与别的距离有什么不同或者相同之处，都要明确，比如说是适用在迁移学习还是适用在NLP还是什么。**



# INTRODUCTION


* 距离 这个概念 在机器学习、深度学习、迁移学习中都非常常见，而且，距离是有各种各样的，在某些情况下用这个，在某些情况下用哪个，各种不同，因此，把距离抽出来 好好总结一下。后续如果还牵涉到一些距离的知识，直接引用本篇的。


**下面是我从 迁移学习 手册 复制过来的，需要 把所有的关于距离的都融入过来。**




### 常见的几种距离


1. 欧氏距离

定义在两个向量(两个点)上：点 \(\mathbf{x}\) 和点 \(\mathbf{y}\) 的欧氏距离为：

\begin{equation}
\label{eq-dist-eculidean}
d_{Euclidean}=\sqrt{(\mathbf{x}-\mathbf{y})^\top (\mathbf{x}-\mathbf{y})}
\end{equation}

2. 闵可夫斯基距离

Minkowski distance， 两个向量（点）的 \(p\) 阶距离：

\begin{equation}
\label{eq-dist-minkowski}
d_{Minkowski}=(|\mathbf{x}-\mathbf{y}|^p)^{1/p}
\end{equation}

当 \(p=1\) 时就是曼哈顿距离，当 \(p=2\) 时就是欧氏距离。

3. 马氏距离

定义在两个向量(两个点)上，这两个点在同一个分布里。点 \(\mathbf{x}\) 和点 \(\mathbf{y}\) 的马氏距离为：

\begin{equation}
\label{eq-dist-maha}
d_{Mahalanobis}=\sqrt{(\mathbf{x}-\mathbf{y})^\top \Sigma^{-1} (\mathbf{x}-\mathbf{y})}
\end{equation}

其中， \(\Sigma\) 是这个分布的协方差。

当 \(\Sigma=\mathbf{I}\) 时，马氏距离退化为欧氏距离。


### 相似度


1. 余弦相似度

衡量两个向量的相关性(夹角的余弦)。向量 \(\mathbf{x},\mathbf{y}\) 的余弦相似度为：

\begin{equation}
\label{eq-dist-cosine}
\cos (\mathbf{x},\mathbf{y}) = \frac{\mathbf{x} \cdot \mathbf{y} }{|\mathbf{x}|\cdot |\mathbf{y}|}
\end{equation}

余弦相似度也被一些迁移学习研究所使用。比如发表在2009年UbiComp上的文章~\cite{zheng2009cross}。

2. 互信息

定义在两个概率分布 \(X,Y\) 上， \(x \in X, y \in Y\) 。它们的互信息为：

\begin{equation}
\label{eq-dist-info}
I(X;Y)=\sum_{x \in X} \sum_{y \in Y} p(x,y) \log \frac{p(x,y)}{p(x)p(y)}
\end{equation}

3. 皮尔逊相关系数

衡量两个随机变量的相关性。随机变量 \(X,Y\) 的Pearson相关系数为：

\begin{equation}
\label{eq-dist-pearson}
\rho_{X,Y}=\frac{Cov(X,Y)}{\sigma_X \sigma_Y}
\end{equation}

理解：协方差矩阵除以标准差之积。

范围： \([-1,1]\) ，绝对值越大表示（正/负）相关性越大。

4. Jaccard相关系数

对两个集合 \(X,Y\) ，判断他们的相关性，借用集合的手段：

\begin{equation}
\label{eq-dist-jaccard}
J=\frac{X \cap Y}{X \cup Y}
\end{equation}

理解：两个集合的交集除以并集。

扩展：Jaccard距离= \(1-J\) 。


### KL散度与JS距离


KL散度和JS距离是迁移学习中被广泛应用的度量手段。

1. KL散度

Kullback–Leibler divergence，又叫做相对熵，衡量两个概率分布 \(P(x),Q(x)\) 的距离：

\begin{equation}
\label{eq-dist-kl}
D_{KL}(P||Q)=\sum_{i=1} P(x) \log \frac{P(x)}{Q(x)}
\end{equation}

这是一个非对称距离： \(D_{KL}(P||Q) \ne D_{KL}(Q||P)\) .

2. JS距离

Jensen–Shannon divergence，基于KL散度发展而来，是对称度量：

\begin{equation}
\label{eq-dist-js}
JSD(P||Q)= \frac{1}{2} D_{KL}(P||M) + \frac{1}{2} D_{KL}(Q||M)
\end{equation}

其中 \(M=\frac{1}{2}(P+Q)\) 。


### 最大均值差异MMD


最大均值差异是迁移学习中使用频率最高的度量。Maximum mean discrepancy，它度量在再生希尔伯特空间中两个分布的距离，是一种核学习方法。两个随机变量的MMD平方距离为

\begin{equation}
\label{eq-dist-mmd}
MMD^2(X,Y)=\left \Vert \sum_{i=1}^{n_1}\phi(\mathbf{x}_i)- \sum_{j=1}^{n_2}\phi(\mathbf{y}_j) \right \Vert^2_\mathcal{H}
\end{equation}

其中 \(\phi(\cdot)\) 是映射，用于把原变量映射到再生核希尔伯特空间(Reproducing Kernel Hilbert Space, RKHS)~\cite{borgwardt2006integrating}中。什么是RKHS？形式化定义太复杂，简单来说希尔伯特空间是对于函数的内积完备的，而再生核希尔伯特空间是具有再生性 \(\langle K(x,\cdot),K(y,\cdot)\rangle_\mathcal{H}=K(x,y)\) 的希尔伯特空间。就是比欧几里得空间更高端的。将平方展开后，RKHS空间中的内积就可以转换成核函数，所以最终MMD可以直接通过核函数进行计算。

理解：就是求两堆数据在RKHS中的均值的距离。

Multiple-kernel MMD：多核的MMD，简称MK-MMD。现有的MMD方法是基于单一核变换的，多核的MMD假设最优的核可以由多个核线性组合得到。多核MMD的提出和计算方法在文献~\cite{gretton2012optimal}中形式化给出。MK-MMD在许多后来的方法中被大量使用，最著名的方法是DAN~\cite{long2015learning}。我们将在后续单独介绍此工作。


### Principal Angle


也是将两个分布映射到高维空间(格拉斯曼流形)中，在流形中两堆数据就可以看成两个点。Principal angle是求这两堆数据的对应维度的夹角之和。

对于两个矩阵 \(\mathbf{X},\mathbf{Y}\) ，计算方法：首先正交化(用PCA)两个矩阵，然后：

\begin{equation}
\label{eq-dist-pa}
PA(\mathbf{X},\mathbf{Y})=\sum_{i=1}^{\min(m,n)} \sin \theta_i
\end{equation}

其中 \(m,n\) 分别是两个矩阵的维度， \(\theta_i\) 是两个矩阵第 \(i\) 个维度的夹角， \(\Theta=\{\theta_1,\theta_2,\cdots,\theta_t\}\) 是两个矩阵SVD后的角度：

\begin{equation}
\mathbf{X}^\top\mathbf{Y}=\mathbf{U} (\cos \Theta) \mathbf{V}^\top
\end{equation}


### A-distance


\(\mathcal{A}\) -distance是一个很简单却很有用的度量。文献\cite{ben2007analysis}介绍了此距离，它可以用来估计不同分布之间的差异性。 \(\mathcal{A}\) -distance被定义为建立一个线性分类器来区分两个数据领域的hinge损失(也就是进行二类分类的hinge损失)。它的计算方式是，我们首先在源域和目标域上训练一个二分类器 \(h\) ，使得这个分类器可以区分样本是来自于哪一个领域。我们用 \(err(h)\) 来表示分类器的损失，则 \(\mathcal{A}\) -distance定义为：

\begin{equation}
\label{eq-dist-adist}
\mathcal{A}(\mathcal{D}_s,\mathcal{D}_t) = 2(1 - 2 err(h))
\end{equation}

\(\mathcal{A}\) -distance通常被用来计算两个领域数据的相似性程度，以便与实验结果进行验证对比。


### Hilbert-Schmidt Independence Criterion


希尔伯特-施密特独立性系数，Hilbert-Schmidt Independence Criterion，用来检验两组数据的独立性：
\begin{equation}
HSIC(X,Y) = trace(HXHY)
\end{equation}
其中 \(X,Y\) 是两堆数据的kernel形式。


### Wasserstein Distance


Wasserstein Distance是一套用来衡量两个概率分部之间距离的度量方法。该距离在一个度量空间 \((M,\rho)\) 上定义，其中 \(\rho(x,y)\) 表示集合 \(M\) 中两个实例 \(x\) 和 \(y\) 的距离函数，比如欧几里得距离。两个概率分布 \(\mathbb{P}\) 和 \(\mathbb{Q}\) 之间的 \(p{\text{-th} }\) Wasserstein distance可以被定义为

\begin{equation}
W_p(\mathbb{P}, \mathbb{Q}) = \Big(\inf_{\mu \in \Gamma(\mathbb{P}, \mathbb{Q}) } \int \rho(x,y)^p d\mu(x,y) \Big)^{1/p},
\end{equation}

其中 \(\Gamma(\mathbb{P}, \mathbb{Q})\) 是在集合 \(M\times M\) 内所有的以 \(\mathbb{P}\) 和 \(\mathbb{Q}\) 为边缘分布的联合分布。著名的Kantorovich-Rubinstein定理表示当 \(M\) 是可分离的时候，第一Wasserstein distance可以等价地表示成一个积分概率度量(integral probability metric)的形式

\begin{equation}
W_1(\mathbb{P},\mathbb{Q})= \sup_{\left \| f \right \|_L \leq 1} \mathbb{E}_{x \sim \mathbb{P} }[f(x)] - \mathbb{E}_{x \sim \mathbb{Q} }[f(x)],
\end{equation}
其中 \(\left \| f \right \|_L = \sup{|f(x) - f(y)|} / \rho(x,y)\) 并且 \(\left \| f \right \|_L \leq 1\) 称为 \(1-\) 利普希茨条件。






# REF

1. [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)
