在数字图像中，往往存在着一些特殊形状的几何图形，像检测马路边一条直线，检测人眼的圆形等等，有时我们需要把这些特定图形检测出来，hough变换就是这样一种检测的工具。

Hough变换的原理是将特定图形上的点变换到一组参数空间上，根据参数空间点的累计结果找到一个极大值对应的解，那么这个解就对应着要寻找的几何形状的参数（比如说直线，那么就会得到直线的斜率k与常熟b，圆就会得到圆心与半径等等）。

关于hough变换，核心以及难点就是关于就是有原始空间到参数空间的变换上。以直线检测为例，假设有一条直线L，原点到该直线的垂直距离为p，垂线与x轴夹角为\theta，那么这条直线是唯一的，且直线的方程为 \rho=xcos\theta+ysin\theta, 如下图所示：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/I9614hb94g.png?imageslim)


可以看到的是这条直线在极坐标系下只有一个(\rho,\theta)与之对应，随便改变其中一个参数的大小，变换到空间域上的这个直线将会改变。好了，再回来看看这个空间域上的这条直线上的所有点吧，你会发现，这条直线上的所有点都可以是在极坐标为(\rho,\theta)所表示的直线上的，为什么说是都可以在，因为其中随便的一个点也可以在其他的(\rho,\theta)所表示的直线上，就比如上述的(x,y)吧，它可以再很多直线上，准确的说，在经过这个点的直线上，随便画两条如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/d0mL3LB3I8.png?imageslim)

可以看到，光是空间上的一个点在极坐标系下就可能在很多极坐标对所对应的直线上，具体有多少个极坐标对呢？那得看你的\theta的步长了，我们可以看到\theta无非是从0-360度（0-2\pi）变化，假设我们没10度一走取一个直线（这个点在这个直线上），那么我们走一圈是不是取了36条直线，也就对应36个极坐标对没错吧，那么这个极坐标对，画在坐标轴上是什么样子的呢？因为\theta是从0-2\pi，并且一个点定了，如果一个\theta也定了，你想想它对应的直线的\rho会怎么样，自然也是唯一的。那么这个点在极坐标下对应的(\rho,\theta)画出来一个周期可能就是这样的，以\theta为x轴的话：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/aeeI7Difd3.png?imageslim)

ok前面说的是单单这一个点对应的极坐标系下的参数对，那么如果每个点都这么找一圈呢？也就是每个点在参数空间上都对应一系列参数对吧，现在把它们华仔同一个坐标系下会怎么样呢？为了方便，假设在这个直线上取3个点画一下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/feIDHa0DD5.png?imageslim)

那么可以看到，首先对于每一个点，在极坐标下，会存在一个周期的曲线来表示通过这个点，其次，这三个极坐标曲线同时经过一个点，要搞清楚的是，极坐标上每一个点对(\rho,\theta)在空间坐标上都是对应一条直线的。好了，同时经过的这一个点有什么含义呢？它表示在空间坐标系下，有一条直线可以经过点1，经过点2，经过点3，这是什么意思？说明这三个点在一条直线上吧。反过来再来看这个极坐标系下的曲线，那么我们只需要找到交点最多的点，把它返回到空间域就是这个需要找的直线了。为什么是找相交最多的点，因为上面这只是三个点的曲线，当空间上很多点都画出来的时候，那么相交的点可能就不知上述看到的一个点了，可能有多个曲线相交点，但是有一点，势必是一条直线上的所有点汇成的交点是曲线相交次数最多的。

再来分析这个算法。可以看到hough变换就是参数映射变换。对每一个点都进行映射，并且每一个映射还不止一次，(\rho,\theta)都是存在步长的，像一个点映射成一个(\rho,\theta)，以\theta取步长为例，当\theta取得步长大的时候，映射的(\rho,\theta)对少些，反之则多，但是我们有看到，映射后的点对是需要求交点的，上述画出来的曲线是连续的，然而实际上因为\theta步长的存在，他不可能是连续的，像上上个图一样，是离散的。那么当\theta步长取得比较大的时候，你还想有很多交点是不可能的，因为这个时候是离散的曲线然后再去相交，所以说\theta步长不能太大，理论上是越小效果越好，因为越小，越接近于连续曲线，也就越容易相交，但是越小带来的问题就是需要非常多的内存，计算机不会有那么多内存给你的，并且越小，计算量越大，想想一个点就需要映射那么多次，每次映射是需要计算的，耗时的。那么再想想对于一副图像所有点都进行映射，随便假设一副100*100的图像（很小吧），就有10000个点，对每个点假设就映射36组(\rho,\theta)参数（此时角度的步长是10度了，10度，已经非常大的一个概念了），那么总共需要映射360000次，在考虑每次映射计算的时间吧。可想而知，hough是多么耗时耗力。所以必须对其进行改进。首先就是对图像进行改进，100*100的图像，10000个点，是不是每个点都要计算？大可不必，我们只需要在开始把图像进行一个轮廓提取，一般使用canny算子就可以，生成黑白二值图像，白的是轮廓，那么在映射的时候，只需要把轮廓上的点进行参数空间变换，为什么提轮廓？想想无论检测图像中存在的直线呀圆呀，它们都是轮廓鲜明的。那么需要变换的点可能就从10000个点降到可能1000个点了，这也就是为什么看到许多hough变换提取形状时为什么要把图像提取轮廓，变成二值图像了。

继续算法，分析这么多，可想而知那么一个hough变换在算法设计上就可以如下步骤：
（1）将参数空间(\rho,\theta)量化，赋初值一个二维矩阵M，M(\rho,\theta)就是一个累加器了。
（2）然后对图像边界上的每一个点进行变换，变换到属于哪一组(\rho,\theta)，就把该组(\rho,\theta)对应的累加器数加1，这里的需要变换的点就是上面说的经过边缘提取以后的图像了。
（3）当所有点处理完成后，就来分析得到的M(\rho,\theta)，设置一个阈值T，认为当M(\rho,\theta)>T，就认为存在一条有意义的直线存在。而对应的M(\rho,\theta)就是这组直线的参数，至于T是多少，自己去式，试的比较合适为止。
（4）有了M(\rho,\theta)和点（x,y）计算出来这个直线就ok了。

说了这么多，这就是原理上hough变换的最底层原理，事实上完全可以自己写程序去实现这些，然而，也说过，hough变换是一个耗时耗力的算法，自己写循环实现通常很慢，曾经用matlab写过这个，也有实际的hough变换例子可以看看：

**虹膜识别（三）：Hough变换检测内圆边缘**http://blog.csdn.net/on2way/article/details/40790559

那么我们在实际中大可不必自己写，opencv已经集成了hough变换的函数，调用它的函数效率高，也很简单。
Opencv中检测直线的函数有cv2.HoughLines()，cv2.HoughLinesP()
函数cv2.HoughLines()返回值有三个（opencv 3.0），实际是个二维矩阵，表述的就是上述的(\rho,\theta)，其中\rho的单位是像素长度（也就是直线到图像原点(0,0)点的距离），而\theta的单位是弧度。这个函数有四个输入，第一个是二值图像，上述的canny变换后的图像，二三参数分别是\rho和\theta的精确度，可以理解为步长。第四个参数为阈值T，认为当累加器中的值高于T是才认为是一条直线。自己画了个图实验一下：

```
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('line.jpg')
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)#灰度图像
#open to see how to use: cv2.Canny
#http://blog.csdn.net/on2way/article/details/46851451
edges = cv2.Canny(gray,50,200)
plt.subplot(121),plt.imshow(edges,'gray')
plt.xticks([]),plt.yticks([])
#hough transform
lines = cv2.HoughLines(edges,1,np.pi/180,160)
lines1 = lines[:,0,:]#提取为为二维
for rho,theta in lines1[:]:
a = np.cos(theta)
b = np.sin(theta)
x0 = a*rho
y0 = b*rho
x1 = int(x0 + 1000*(-b))
y1 = int(y0 + 1000*(a))
x2 = int(x0 - 1000*(-b))
y2 = int(y0 - 1000*(a))
cv2.line(img,(x1,y1),(x2,y2),(255,0,0),1)

plt.subplot(122),plt.imshow(img,)
plt.xticks([]),plt.yticks([])123456789101112131415161718192021222324252627
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/hEKE15AjHL.png?imageslim)


测试一个新的图，不停的改变 cv2.HoughLines最后一个阈值参数到合理的时候如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/C7a2bcG7i9.png?imageslim)

可以看到检测的还可以的。

函数cv2.HoughLinesP()是一种概率直线检测，我们知道，原理上讲hough变换是一个耗时耗力的算法，尤其是每一个点计算，即使经过了canny转换了有的时候点的个数依然是庞大的，这个时候我们采取一种概率挑选机制，不是所有的点都计算，而是随机的选取一些个点来计算，相当于降采样了。这样的话我们的阈值设置上也要降低一些。在参数输入输出上，输入不过多了两个参数：minLineLengh(线的最短长度，比这个短的都被忽略)和MaxLineCap（两条直线之间的最大间隔，小于此值，认为是一条直线）。输出上也变了，不再是直线参数的，这个函数输出的直接就是直线点的坐标位置，这样可以省去一系列for循环中的由参数空间到图像的实际坐标点的转换。

```
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('room.jpg')
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)#灰度图像
#open to see how to use: cv2.Canny
#http://blog.csdn.net/on2way/article/details/46851451
edges = cv2.Canny(gray,50,200)
plt.subplot(121),plt.imshow(edges,'gray')
plt.xticks([]),plt.yticks([])
#hough transform
lines = cv2.HoughLinesP(edges,1,np.pi/180,30,minLineLength=60,maxLineGap=10)
lines1 = lines[:,0,:]#提取为二维
for x1,y1,x2,y2 in lines1[:]:
    cv2.line(img,(x1,y1),(x2,y2),(255,0,0),1)

plt.subplot(122),plt.imshow(img,)
plt.xticks([]),plt.yticks([])12345678910111213141516171819
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/KA71Da9JL9.png?imageslim)

可以看到这个方法似乎更好些，速度还快，调参数得到较好的效果就ok了。

Ok说完了直线的检测，再来说说圆环的检测，我们知道圆的数学表示为：

(x-x_{center})^2+(y-y_{center})^2 = r^2



从而一个圆的确定需要三个参数，那么就需要三层循环来实现（比直线的多一层），从容把图像上的所有点映射到三维参数空间上。其他的就一样了，寻找参数空间累加器的最大（或者大于某一阈值）的值。那么理论上圆的检测将比直线更耗时，然而opencv对其进行了优化，用了一种霍夫梯度法，感兴趣去研究吧，我们只要知道它能优化算法的效率就可以了。关于函数参数输入输出：



cv2.HoughCircles(image, method, dp, minDist, circles, param1, param2, minRadius, maxRadius)



这个时候输入为灰度图像，同时最好规定检测的圆的最大最小半径，不能盲目的检测，否侧浪费时间空间。输出就是三个参数空间矩阵。



来个实际点的人眼图像，把minRadius和maxRadius调到大圆范围检测如下：

```
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread('eye.jpg')
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)#灰度图像

plt.subplot(121),plt.imshow(gray,'gray')
plt.xticks([]),plt.yticks([])
#hough transform
circles1 = cv2.HoughCircles(gray,cv2.HOUGH_GRADIENT,1,
100,param1=100,param2=30,minRadius=200,maxRadius=300)
circles = circles1[0,:,:]#提取为二维
circles = np.uint16(np.around(circles))#四舍五入，取整
for i in circles[:]:
    cv2.circle(img,(i[0],i[1]),i[2],(255,0,0),5)#画圆
    cv2.circle(img,(i[0],i[1]),2,(255,0,255),10)#画圆心

plt.subplot(122),plt.imshow(img)
plt.xticks([]),plt.yticks([])1234567891011121314151617181920
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/II46FhmFeE.png?imageslim)


把半径范围调小点，检测内圆：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/EGH72ldc1B.png?imageslim)

至此圆的检测就是这样。




## REF

- [我i智能  opencv+python](https://blog.csdn.net/on2way/article/category/5630773)
