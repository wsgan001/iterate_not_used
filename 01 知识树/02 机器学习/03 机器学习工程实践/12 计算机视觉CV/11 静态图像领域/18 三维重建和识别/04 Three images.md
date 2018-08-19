


# Three-image

更 robust 了。

Three-image 的时候：



Three-image 2D-to-3D

reconstruction method

- 更加健壮了。 More robust using 3 views
- 包含了3个极坐标的关系。It contains 3 epipolar relations
    - Stereo1: view 1,2,
    - Stereo2: view 2,3,
    - Stereo3 :view 3,1.
- Combine 3 epipolar geometry information together.
- Similar to the algorithm in epipolar geometry (apply 3 times)

下面这个连接讲了如何结合的：

http://www.cs.unc.edu/~marc/tutorial/node45.html



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/f7kd9k2G7b.png?imageslim)
M=3-D model point
M, m’, m” are image points
C,C’,C” are camera centers





Example of 3-image
reconstruction
l Example

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/1DekjGlgCj.png?imageslim)


LIBVISO: Feature Matching for Visual Odometry
http://www.youtube.com/watch?v=DPLh6MoxPAk



<span style="color:red;">老师也没怎么讲</span>





## REF

- 七月在线 opencv计算机视觉
