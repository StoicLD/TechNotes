# CVVtuber原理简介

## 零，初始步骤

从Readme可以看到。

主要是分为六个步骤。

（1）将**WebCamTexture**转换为OpenCV的**Mat**数据结构。

（2）从**Mat**中检测面部特征点。

（3）从面部特征点中估算**头部转动角度**。

（4）使用得到的**头部转动角度**控制3D模型转动。

（5）使用得到的**头部转动角度**设置**Animator.SetLookAtPosition()**方法。

（6）使用**面部特征点**控制3D模型的BlendShape。

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200312153102.png)

Live2D脚本中处理顺序如下

![](https://cdn.jsdelivr.net/gh/JokerLD/Image/Other/20200312153036.png)

controller类以此初始化这六个脚本



## 一，初始化

前三步使用的都是OpenCV for Unity和Dlib的东西。

后面三步是使用Live2D的API



## 二，Update逻辑

这一步需要关注，有哪些参数被更新调整

### （1）OpenCV



### （2）Dlib



### （3）Cubism Live2D

关于面部模型的参数，需要参考一下API。

Live2D模型是怎么控制的。有哪些参数起作用？原理是什么？



