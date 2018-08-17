
# Two-image

Two-image 2D-to-3D reconstruction
method: stereo vision，就是 一个 matching 的过程


- Objectives:
    - Basic idea of stereo vision
    - Stereo reconstruction by epipolar geometry (极坐标)
        - Stereo camera pair calibration (find Fundamental matrix F)
        - Construct the 3D (graphic) model from 2 images


一般来说我们有两种方法，一种是极坐标，

如果这两个camera 的运动是平移的，

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/fjiJ4I19AG.png?imageslim)

<span style="color:red;">上面图中的x 指的是什么？第一个式子中的 x 与第二个式子中的 x 指的是相同的东西吗？</span>

所以，可以求出现实中的深度信息。

但是如果 camera 位置不是平行的呢？

这个可以用极坐标来做：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/KbJ49djGcf.png?imageslim)

<span style="color:red;">关于这个的计算老师没有讲，还是要自己补充进去的。</span>


有一个方法：

Method: 8-point algorithm
http://www.cs.manchester.ac.uk/ugt/COMP37111/papers/Hartley.pdf
- Find 8 point corresponded
    - Map 8 Right_image_points to left_image_point
- Solve the epeiolar formula
    - Right_image_pointT*E*left_image_point=0
    - Find E.
    - From E we can find camera R (rotation) ,T (translation)
    - From R,T we can find model (3D positions of the left feature points (using left as reference)

通过这个方法有一个叫 8-point algorithm，首先要找到右边的8个图像上的点的对应的左边的8个图像上的点。然后解这个公式，就可以找到这个camera的 R。


An example of stereo reconstruction

Short-Baseline Stereo
Systems for Mobile Devices

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/e02GdJHdHB.png?imageslim)


http://www.lelaps.de/videos.html#SQx5vU8BA-M
http://www.lelaps.de/projects/stmobile.html

<span style="color:red;">上面这两个例子真的震惊了，导航原来已经做到这个程度了，厉害。没准这还是几年前的水平，怪不得现在自动驾驶已经基本成熟了。真厉害。</span>

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/2amj53Da71.png?imageslim)

camera 在不同的移动，光斑就是进行匹配，在右边生成深度信息。

<span style="color:red;">感觉只是提了下，基本没有怎么说。</span>




Stereo-based Free-space Estimation

Another example

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/ahGC14fmLL.png?imageslim)

http://www.lelaps.de/videos.html#VrKBNtAN03o (demo)
http://www.lelaps.de/projects/freespace.html
