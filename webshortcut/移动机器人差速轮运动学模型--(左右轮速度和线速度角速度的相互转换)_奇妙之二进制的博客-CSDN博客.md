# 移动机器人差速轮运动学模型--(左右轮速度和线速度角速度的相互转换)_奇妙之二进制的博客-CSDN博客

做[机器人](http://lib.csdn.net/base/robot)底层程序的时候，经常用到航迹推演（Odometry），无论是定位导航还是普通的方向控制。航迹推演中除了对机器人位姿进行估计，另一个很重要的关系是移动机器人前进速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;v)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;v)、转向角速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;w)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;w)与左轮速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;v_%7Bl%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;v_%7Bl%7D)、右轮速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;v_%7Br%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;v_%7Br%7D)之间的转换。

在[机器人局部路径规划算法DWA解析](http://blog.csdn.net/heyijia0327/article/details/44983551)一文中，是在假设已知机器人前进线速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;v)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;v)和角速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;w)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;w)的情况下，对机器人航迹推演的位姿进行推导了，然而缺少如何通过左右轮速度得到![](http://latex.codecogs.com/png.latex?%5Cinline&space;v)
、[![](http://latex.codecogs.com/png.latex?%5Cinline&space;w)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;w)，因此本文将补上这个空缺。

下图是移动机器人在两个相邻时刻的位姿，其中[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Ctheta&space;_%7B1%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Ctheta&space;_%7B1%7D)是两相邻时刻移动机器人绕圆弧运动的角度，[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Ctheta&space;_%7B3%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Ctheta&space;_%7B3%7D)是两相邻时刻移动机器航向角（朝向角head）的变化量。[![](http://latex.codecogs.com/png.latex?%5Cinline&space;l)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;l)是左右轮之间的间距，[![](http://latex.codecogs.com/png.latex?%5Cinline&space;d)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;d)是右轮比左轮多走的距离。[![](http://latex.codecogs.com/png.latex?%5Cinline&space;r)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;r)是移动机器人圆弧运动的半径。

![](https://img-blog.csdn.net/20150723145732853)

移动机器人前进速度等于左右轮速度的平均，这个好理解。

[![](http://latex.codecogs.com/png.latex?%5Cdpi%7B120%7D&space;v=%5Cfrac%7Bv_%7Br%7D+v_%7Bl%7D%7D%7B2%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cdpi%7B120%7D&space;v=%5Cfrac%7Bv_%7Br%7D+v_%7Bl%7D%7D%7B2%7D) (1)

现在来推导机器人航向角如何计算，以及如何计算角速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;w)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;w)。如图所示，把两个时刻的机器人位置叠加在一起，可以清楚的看到移动机器人航向角变化量是[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Ctheta&space;_%7B3%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Ctheta&space;_%7B3%7D)。从图中的几何关系可以得到：

[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Cdpi%7B120%7D&space;%5Ctheta_%7B3%7D&space;=&space;%5Ctheta_%7B2%7D=%5Ctheta_%7B1%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Cdpi%7B120%7D&space;%5Ctheta_%7B3%7D&space;=&space;%5Ctheta_%7B2%7D=%5Ctheta_%7B1%7D)

也就是说移动机器人航向角变化了多少角度，它就绕其运动轨迹的圆心旋转了多少角度。这句话很好验证，我们让机器人做圆周运动，从起点出发绕圆心一圈回到起点处，在这过程中机器人累计的航向角为360度，同时它也确实绕轨迹圆心运动了360度，说明机器人航向角变化多少度，就绕圆心旋转了多少度。而这三个角度中，[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Ctheta&space;_%7B2%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Ctheta&space;_%7B2%7D)很容易计算出来，由于相邻时刻时间很短，角度变化量[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Ctheta&space;_%7B2%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Ctheta&space;_%7B2%7D)很小，有下面的近似公式：

[![](http://latex.codecogs.com/png.latex?%5Cinline&space;%5Cdpi%7B150%7D&space;%5Ctheta_%7B2%7D%5Capprox&space;sin%5Cleft&space;%28&space;%5Ctheta&space;%5Cright&space;%29=%5Cfrac%7Bd%7D%7Bl%7D=%5Cfrac%7B%28v_%7Br%7D-&space;v_%7Bl%7D%29%5Ccdot&space;%5CDelta&space;t%7D%7Bl%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;%5Cdpi%7B150%7D&space;%5Ctheta_%7B2%7D%5Capprox&space;sin%5Cleft&space;%28&space;%5Ctheta&space;%5Cright&space;%29=%5Cfrac%7Bd%7D%7Bl%7D=%5Cfrac%7B%28v_%7Br%7D-&space;v_%7Bl%7D%29%5Ccdot&space;%5CDelta&space;t%7D%7Bl%7D)

所以可以得到机器人绕圆心运动的角速度[![](http://latex.codecogs.com/png.latex?%5Cinline&space;w)
](http://private.codecogs.com/eqnedit.php?latex=%5Cinline&space;w)，它也是机器人航向角变化的速度：

[![](http://latex.codecogs.com/gif.latex?%5Cdpi%7B120%7D&space;w=&space;%5Cfrac%7B%5Ctheta_%7B1%7D%7D%7B%5CDelta&space;t%7D=%5Cfrac%7Bv_%7Br%7D-&space;v_%7Bl%7D%7D%7Bl%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cdpi%7B120%7D&space;w=&space;%5Cfrac%7B%5Ctheta_%7B1%7D%7D%7B%5CDelta&space;t%7D=%5Cfrac%7Bv_%7Br%7D-&space;v_%7Bl%7D%7D%7Bl%7D) (2)

线速度、角速度都有了，因此可以推出移动机器人圆弧运动的半径：

[![](http://latex.codecogs.com/gif.latex?%5Cdpi%7B120%7D&space;%5Clarge&space;r&space;=&space;%5Cfrac%7Bv%7D%7Bw%7D=%5Cfrac%7Bl%5Ccdot&space;%28v_%7Br%7D+v_%7Bl%7D%29%7D%7B2%5Cleft&space;%28&space;v_%7Br%7D-v_%7Bl%7D&space;%5Cright&space;%29%7D)
](http://private.codecogs.com/eqnedit.php?latex=%5Cdpi%7B120%7D&space;%5Clarge&space;r&space;=&space;%5Cfrac%7Bv%7D%7Bw%7D=%5Cfrac%7Bl%5Ccdot&space;%28v_%7Br%7D+v_%7Bl%7D%29%7D%7B2%5Cleft&space;%28&space;v_%7Br%7D-v_%7Bl%7D&space;%5Cright&space;%29%7D) (3)

从公式（3）可以发现当左轮速度等于右轮速度时，半径无穷大，即直线运动。最后将三个公式综合起来，可以得到左右轮速度和线速度角速度之间的关系如下，：

![](https://img-blog.csdn.net/20150723153151497)