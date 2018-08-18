


## Photo tourism


根据一些图，构建一个教堂场景

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/24dbIGIHcF.png?imageslim)

第二章图是他们拼出来的，第三张图是图库里面的。

http://phototour.cs.washington.edu/

大家如果想从事这个，从上面这个paper看起。

他们用到的最重要的就是 Key point detection 和 matching 在论文的 4.1 章有说，是 什么意思呢？一幅图里面首先要检测出关键点 key point，然后有了matching 之后，求出不同 location 的坐标关系，从而构建出一个物体。

具体是 怎么construct 的呢？公式他论文写在附录里面了。


## A Projector-Camera Systems

<span style="color:red;">基本没讲</span>


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/7hfLlFDl46.png?imageslim)


Projector-Camera calibration

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/BbadfkJ2bE.png?imageslim)

http://www.youtube.com/watch?v=YHhQSglmuqY&feature=channel_page

Our setup

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/759Ag5iGgh.png?imageslim)

Calibration procedure

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/jFmBIe5K6e.png?imageslim)


Quadrangle tracking
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/GCkJbBKC1l.png?imageslim)

Experiments

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/7jI8ic4dkI.png?imageslim)

Projection result

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/BFaLg81heC.png?imageslim)

Results

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/aa2l87jKkH.png?imageslim)

Hand held direct manipulation 3D Display

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/LEH6j8i78B.png?imageslim)

http://www.youtube.com/watch?v=vVW9QXuKfoQ&feature=relmfu

Keystone correction

Configuration

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/k7EjfFCLBj.png?imageslim)


Aim of this work

Desired Results

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/l06cehej4d.png?imageslim)






Overview

- Three major modules
    - Projector-camera pair calibration
    - Projection region detection and tracking
    - Automatic keystone correction

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/73E7h3iebk.png?imageslim)


Pre-warp projection image


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/aK75dI575m.png?imageslim)


http://www.youtube.com/watch?v=y5XYdeh8Bno&list=UUfy2EumiHMeoUorMFR0woZA&index=1&feature=plcp




Keystone correction

Some real correction results


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180817/GCCKEb2gea.png?imageslim)








## REF

- 七月在线 opencv计算机视觉
