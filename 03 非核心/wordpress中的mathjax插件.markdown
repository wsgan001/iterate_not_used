
# 缘由：


博客中使用公式的时候不想使用图片，因为不好修改和统一图片大小和背景，因此还是使用这个mathjax这个插件：


# 要点：




## 1.使用方法：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5a9bfbb69bb18.png)


## 2.在线的LaTeX编辑器，可以在那里可视化编辑好之后拷贝过来。


[LaTeX在线编辑](https://www.codecogs.com/latex/eqneditor.php?lang=zh-cn)


## 3.本地加载尝试


感觉使用CDN的话加载的有点慢，因此试了下讲MathJax-2.7.3放在服务器上加载

下载地址：[MathJax-2.7.3](https://www.mathjax.org/mathjax-v2-7-3-now-available/)


##




## 4.几个公式的例子：


使用：[https://blog.csdn.net/yanxiangtianji/article/details/54767265](https://blog.csdn.net/yanxiangtianji/article/details/54767265)

[mathjax]
At first, we sample \(f(x)\) in the \(N\) (\(N\) is odd) equidistant points around \(x^*\):
\[
f_k = f(x_k),\: x_k = x^*+kh,\: k=-\frac{N-1}{2},\dots,\frac{N-1}{2}
\]
where \(h\) is some step.
Then we interpolate points \(\{(x_k,f_k)\}\) by polynomial
\begin{equation} \label{eq:poly}
P_{N-1}(x)=\sum_{j=0}^{N-1}{a_jx^j}
\end{equation}
Its coefficients \(\{a_j\}\) are found as a solution of system of linear equations:
\begin{equation} \label{eq:sys}
\left\{ P_{N-1}(x_k) = f_k\right\},\quad k=-\frac{N-1}{2},\dots,\frac{N-1}{2}
\end{equation}

\[
f_k = f(x_k)
\]

\[evidence_{i}=\sum_{j}^{ }W_{i,j}x_j+b_i\]





\[aaa\underset{cccc}{\underbrace{mmmm}}bbbb\]



\[\int u \frac{dv}{dx}\,dx=uv-\int \frac{du}{dx}v\,dx\]

\[\sum_{i=1}^{n}{(X_i - \overline{X})^2}\]



\[X_1, \cdots,X_n\]



\[\begin{pmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{pmatrix}\]

\[\begin{pmatrix} a_{11} & \cdots & a_{1n}\\ \vdots & \ddots & \vdots\\ a_{m1} & \cdots & a_{mn} \end{pmatrix}\]





\[\begin{align} \sqrt{37} & = \sqrt{ \frac{73^2-1}{12^2}} \\ & = \sqrt{ \frac{73^2}{12^2} \cdot \frac{73^2-1}{73^2}} \\ & \approx \frac{73}{12} \left(1 - \frac{1}{2 \cdot73^2} \right) \end{align}\]






# COMMENT：





# REF：






  1.


[3分钟教会你用mathjax在csdn博客中编辑数学公式](https://blog.csdn.net/cvrszeng/article/details/52333055)





  2.


[latex公式、编号、对齐](http://blog.sina.com.cn/s/blog_4419b53f0101baiw.html)
