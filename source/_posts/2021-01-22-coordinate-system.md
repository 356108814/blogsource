title: 图形学基础之坐标系
categories: 图形学
tags:
  - 坐标系
  - 图形学
date: 2021-01-22 20:34:37
---

**3D图形学基础系列第 1 篇**

> 坐标系是数学或物理学用语，定义如下： 对于一个n维系统，能够使每一个点和一组n个标量构成一一对应的系统。
> 
> 详情可阅读[维基百科坐标系](https://bk.tw.lvfukeji.com/wiki/%E5%9D%90%E6%A8%99%E7%B3%BB)

## 坐标系的分类

坐标系按种类分有笛卡尔坐标系、极坐标系、圆柱坐标系、球坐标系、齐次坐标系。   
这其中的笛卡尔坐标系也称为直角坐标系，是最常用到的一种坐标系。

<!-- more -->
  
## 游戏中的坐标系

大部分游戏引擎中使用的坐标系为笛卡尔坐标系。  
根据Z轴方向的选择不同，又分左手坐标系和右手坐标系。左手坐标系Z轴正方向朝屏幕里，而右手坐标系Z轴正方向朝屏幕外。

二者之间的唯一区别在于Z轴的方向不同而已。如下图：
{% asset_img 坐标系.png %}

我们所熟知的H5游戏引擎Laya使用的是右手坐标系，而Unity和Unreal则使用的是左手坐标系。

## 世界坐标系与局部坐标系
在3D场景中，世界坐标系就是场景的坐标，XYZ三个轴的方向一直保持不变。  
场景中一个物体的世界坐标P(10, 100, 8)表示的是该物体相对于世界坐标系原点O(0, 0, 0)的坐标。  
局部坐标系则是以物品原点为坐标原点的坐标系，物体自身的旋转不会影响到局部坐标系。

---
坚持长期输出，相信时间的力量


