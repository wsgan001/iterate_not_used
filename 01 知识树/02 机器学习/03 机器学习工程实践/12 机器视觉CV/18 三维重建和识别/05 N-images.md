

# N-image

2D-to-3D reconstruction
(batched method: order of
images can be random )



N-image 2D-to-3D
reconstruction method


- Bundle adjustment approach (光束法平差)
    - Guess iteratively the solution for 3D to explain the measurements of feature points in all images
    - Math: Q(u,v)=g(X), g is nonlinear (projection) because
        - u=fX/Z
        - v=fY/Z, f=focal length
        - Given Q (image measurement) , we want to find X=(X,Y,Z)i from image points (u,v)i of all N model points (i=1,,,N), g is the projection formulas
        - A typical non linear optimization problem,
        - Gauss-Newton for non linear optimization method is used.


基于的是一个 batch 的概念， 使用的是 Bundle adjustment，这个是基本的CV 知识，
简单来说就是在所有的 image 里面找到好多的 feature pooints，不断的迭代，比如先根据两张图找到 Z，然后，再到下一个，不断迭代，最后取平均。是一个加权平均的问题。类似 model asemble。


下面这个图就是介绍 Bundle adjustment 是在做什么：

Batched method: order of images can be random


每个camera 都可以构造出 u、v，From measurement $[u,v]_i$ find X

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/ECG950kmJf.png?imageslim)


这个就是一堆 image 的成像原理


Example
Bundle adjustment reconstruction

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/hdJhbLjGk7.png?imageslim)

http://www.cse.cuhk.edu.hk/%7Ekhwong/demo/canyon2b2.mpg




## Sequential method: order of images are used like in a move



如果是一堆摄像机固定不变，然后对获得的图像进行处理，那么就是 Bundle adjustment。

如果是一台摄像机位置一直在变化，然后把这些位置变化后排到的图像看做是 n 太摄像机，那么再最图像进行处理就是 Kalman Filter。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/JGf82H2c45.png?imageslim)

有一个最先开始的state，由于相机在不断的移动，因此不断循环

Kalman filter example：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/C0B9ja5i2E.png?imageslim)



Kalman filter will make a prediction of the state at next time frame according to the current state.

Since the car has a constant acceleration, we can find a linear relation between the position and the velocity.

We measure the position the car at the next time frame.This measurement will be used to update the Kalman filter so that the prediction will get better in the next frame if the filter converge

<span style="color:red;">这部分老师不了解，也没有怎么说</span>
