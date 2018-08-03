# ai矩阵论-向量求导


TODO

* **再补充下。**




# MOTIVE

* 在线性回归的时候用到了，



# 向量的导数

* \(\frac{\partial A \overrightarrow{x}}{\partial \overrightarrow{x}}=A^T\)
* \(\frac{\partial A^T \overrightarrow{x}}{\partial \overrightarrow{x}}=A\)
* \(\frac{\partial A \overrightarrow{x}}{\partial \overrightarrow{x}^T}=A\)




# 标量对向量的导数


A为\(n\times n\)的矩阵，x为\(n\times 1\)的列向量，记 \(y=\overrightarrow{x}^T\cdot A\cdot \overrightarrow{x}\)

这个时候y是一个数。这时可得：

\[\frac{\partial y}{\partial \overrightarrow{x}}=\frac{\partial(\overrightarrow{x}^T\cdot A\cdot \overrightarrow{x})}{\partial \overrightarrow{x}}=(A^T+A)\cdot \overrightarrow{x}\]

如果A为对称阵，则有：

\[\frac{\partial y}{\partial \overrightarrow{x}}=\frac{\partial(\overrightarrow{x}^T\cdot A\cdot \overrightarrow{x})}{\partial \overrightarrow{x}}=2A\cdot \overrightarrow{x}\]












## REF

1. 七月在线 机器学习
2. [矩阵论：向量求导/微分和矩阵微分](https://blog.csdn.net/pipisorry/article/details/68961388)
