# 内参、外参、畸变参数三种参数与相机的标定方法与相机坐标系的理解_yangdeshun888的博客-CSDN博客_畸变系数
1、 相机参数是三种不同的参数。

 相机的内参数是六个分别为：1/dx、1/dy、r、u0、v0、f。

opencv1 里的说内参数是 4 个其为 fx、fy、u0、v0。实际其 fx=F\*Sx，其中的 F 就是焦距上面的 f,Sx 是像素 / 没毫米即上面的 dx，其是最后面图里的后两个矩阵进行先相乘，得出的，则把它看成整体，就相当于 4 个内参。其是把 r 等于零，实际上也是六个。

dx 和 dy 表示：x 方向和 y 方向的一个像素分别占多少长度单位，即一个像素代表的实际物理值的大小，其是实现图像物理坐标系与像素坐标系转换的关键。u0，v0 表示图像的中心像素坐标和图像原点像素坐标之间相差的横向和纵向像素数。

相机的外参数是 6 个：三个轴的旋转参数分别为（ω、δ、 θ）, 然后把每个轴的 3\*3 旋转[矩阵](https://so.csdn.net/so/search?q=%E7%9F%A9%E9%98%B5&spm=1001.2101.3001.7020)进行组合（即先矩阵之间相乘），得到集合三个轴旋转信息的 R，其大小还是 3\*3；T 的三个轴的平移参数（Tx、Ty、Tz）。R、T 组合成成的 3\*4 的矩阵，其是转换到标定纸坐标的关键。其中绕 X 轴旋转θ，则其如图：

注意：在每个视场无论我们能提取多少个角点，我们只能得到四个有用的角点信息，这四个点可以产生 8 个方程，6 个用于求外参，这样每个视场就还赚两个方程来求内参，则其在多一个视场即可求出 4 个内参。因为六个外参，这就是为什么要消耗三个点用于求外参。

![](https://img-blog.csdn.net/20160512171214927?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

畸变参数是：k1,k2,k3 径向畸变系数，p1,p2 是切向畸变系数。径向畸变发生在相机坐标系转图像物理坐标系的过程中。而切向畸变是发生在相机制作过程，其是由于感光元平面跟透镜不平行。其如下：

**1．径向畸变：** 产生原因是光线在远离透镜中心的地方比靠近中心的地方更加弯曲径向畸变主要包含桶形畸变和枕形畸变两种。下面两幅图是这两种畸变的示意：

![](https://img-blog.csdn.net/20141103095355515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvb250aGV3YXlzdWNjZXNz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

          它们在真实照片中是这样的：

![](https://img-blog.csdn.net/20141103095546952?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvb250aGV3YXlzdWNjZXNz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**2. 切向畸变：** 产生的原因透镜不完全平行于图像平面，这种现象发生于成像仪被粘贴在摄像机的时候。下面图片来自于《学习[opencv](https://so.csdn.net/so/search?q=opencv&spm=1001.2101.3001.7020)》p413。

![](https://img-blog.csdn.net/20141103095720515?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvb250aGV3YXlzdWNjZXNz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其中畸变的形象示意图是如下：

![](https://img-blog.csdn.net/20160929135941127?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其总的转换关系：

![](https://img-blog.csdn.net/20160509220120773?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

下面是普通摄像头标定后的输出值，其如下：

![](https://img-blog.csdn.net/20160824202655212?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其中的 fx、fy 是 f/dx、f/dy 得出来的值。其中的 cx、cy 一般不是正好是图像分辨率的一半，其是有偏差的，一般越好的摄像头则其越接近于分辨率的一半。上面例子使用的摄像头是一个普通的 1280x720 分辨率的摄像头，其偏差还是蛮大的。下面的数据是比较好的摄像头罗技 720p 的，其分辨率也是 1280x720 的分辨率。可以看出其更接近分辨率的一半。

![](https://img-blog.csdn.net/20160824203131517?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

2、相机的标定方法与意义：

（一）什么是摄像机[标定](https://so.csdn.net/so/search?q=%E6%A0%87%E5%AE%9A&spm=1001.2101.3001.7020)

    在图像测量过程以及机器视觉应用中，为确定空间物体表面某点的三维几何位置与其在图像中对应点之间的相互关系，必须建立摄像机成像的几何模型, 这些几何模型参数就是摄像机参数。在大多数条件下这些参数必须通过实验与计算才能得到，这个求解参数的过程就称之为摄像机标定。

（一）相机标定的意义

    无论是在图像测量或者机器视觉应用中，摄像机参数的标定都是非常关键的环节，其标定结果的精度及算法的稳定性直接影响摄像机工作产生结果的准确性。因此，做好摄像机标定是做好后续工作的前提，是提高标定精度是科研工作的重点所在。其标定的目的就是为了相机内参、外参、畸变参数。

  其标定方法大概有三种如下：

![](https://img-blog.csdn.net/20160929120338637?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

1\. 线性标定方法的大概数学公式是：

![](https://img-blog.csdn.net/20160929120500153?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其求解过程如下：

![](https://img-blog.csdn.net/20160929134610209?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

注意：这种标定是没有考虑到相机畸变的非线性问题，意思是这种标定是

在不考虑相机畸变的情况下使用。

2\. 非线性标定方法：

当镜头畸变明显时必须引入畸变模型，将线性标定模型转化为非线性标定模型，

通过非线性优化的方法求解相机参数：

![](https://img-blog.csdn.net/20160929135055217?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

3\. 两步标定法：

   1.Tsai 的经典两步法

       概念：Tsai 基于 RAC 约束（RadialAlignment Constrain）提出的两步法，在求解过程中将 CCD（电耦合器件）阵列感光元的横向间距和纵向间距当作已知参数，求解的摄像机内部参数：有效焦距 f；镜头径向畸变系数 k1,k2；非确定性尺度因子 xs ；图像中心或主点 u0,v0。外部参数：世界坐标系与摄像机坐标系之间的旋转矩阵 R 与平移向量 t。

求解：首先利用最小二乘法求解超定线性方程组，求得模型外部参数；然后求解内部参数，如果摄像机无透镜畸变，可通过一个超定线性方程组解出，如果存在一个以二次多项式近似的径向畸变，则利用一个包含三个变量的目标函数进行优化搜索求解。

1\. 相机坐标系：

![](http://image71.360doc.com/DownloadImg/2014/04/1014/40652696_5.jpg)
相机坐标系是连接图像物理坐标系与世界坐标系的桥梁，其中相机坐标的系的坐标原点是：镜头的光心--- 其也是相机坐标系里的投影中心。

![](https://img-blog.csdn.net/20160929142319513?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**参考文献：** 

[**像素坐标转换到标定纸坐标的过程**](http://blog.csdn.net/wangxiaokun671903/article/details/37966891) 
 [https://blog.csdn.net/yangdashi888/article/details/51356385](https://blog.csdn.net/yangdashi888/article/details/51356385)
