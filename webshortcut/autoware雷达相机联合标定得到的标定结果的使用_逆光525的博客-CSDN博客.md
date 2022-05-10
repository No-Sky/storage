# autoware雷达相机联合标定得到的标定结果的使用_逆光525的博客-CSDN博客
在使用 autoware 完成了相机和雷达的联合标定后标定工作，得到如下标定文件。但参考这篇博客[https://blog.csdn.net/mxdsdo09/article/details/88582588，发现这个标定文件不同于一般标定文件不能直接用于手动投影。](https://blog.csdn.net/mxdsdo09/article/details/88582588，发现这个标定文件不同于一般标定文件不能直接用于手动投影。)  
![](https://img-blog.csdnimg.cn/20191210162442435.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMDU5ODQz,size_16,color_FFFFFF,t_70)

## 1、一般标定结果的使用

一般而言得到标定文件后可以借助于 opencv 的 ProjectPoints2 函数实现手动投影以便于查看标定结果的好坏。函数如下：

```cpp
cv::projectPoints(pts_3d, r_vec, t_vec, camera_matrix, distortion_coeff, pts_2d);

```

可知完成投影需要的参数依次为：pcd 得到的三维坐标、旋转矩阵或者旋转向量、平移矩阵、相机参数、畸变矩阵。

旋转矩阵和平移矩阵有时以 4x4 的矩阵形式给出，如下，如 autoware 标定结果的 CameraExtrinsicMat。  
\[ R T 0 1 ] \\left\[

RT01

\\right] \[R0​T1​]  
其中 R 具体形式如下：  
\[ f x 0 c x 0 f x c y 0 0 1 ] \\left\[

fx0cx0fxcy001

\\right] ⎣⎡​fx00​0fx0​cxcy1​⎦⎤​

### 1.1、罗德里格斯变换

有时需要将旋转矩阵经过罗德里格斯变换转化为旋转向量使用。具体方法一堆，代码如下，选装向量转旋转矩阵同理：

```python
import cv2
import numpy as np


R = np.float64([[-0.036255, 0.978364, -0.203692],
                [0.998304,0.026169,-0.051994],
                [-0.045539,-0.205232,-0.977653]])
r=cv2.Rodrigues(R)
print(r[0])

```

得到旋转向量为：  
\[\[-2.10041703]  
\[-2.16779617]  
\[ 0.27332964]]

## 2、autoware 标定结果的特殊性

### 2.1、旋转向量

autoware 在得到旋转矩阵后进行了如下操作：

```cpp
cv::Mat camera_velodyne_rotation = rotation_matrix.t();

```

所以真正的旋转矩阵应该将 autoware 标定结果给出的旋转矩阵进行转置操作。以本文标定文件为例应该为：  
\[ − 4.8853956324459435 e − 03 − 9.9997850714377279 e − 01 − 4.3724318206638246 e − 03 1.5077370169795101 e − 02 4.2983278748005560 e − 03 − 9.9987709108982181 e − 01 9.9987439502083342 e − 01 − 4.9507199468951657 e − 03 1.5056047081819290 e − 02 ] \\left\[

−4.8853956324459435e−03−9.9997850714377279e−01−4.3724318206638246e−031.5077370169795101e−024.2983278748005560e−03−9.9987709108982181e−019.9987439502083342e−01−4.9507199468951657e−031.5056047081819290e−02

\\right] ⎣⎡​−4.8853956324459435e−031.5077370169795101e−029.9987439502083342e−01​−9.9997850714377279e−014.2983278748005560e−03−4.9507199468951657e−03​−4.3724318206638246e−03−9.9987709108982181e−011.5056047081819290e−02​⎦⎤​  
相应的旋转向量为：  
\[ 1.19258089 , -1.20375297 , 1.21670938 ]

### 2.2、平移向量

autoware 在得到平移矩阵后进行了如下操作：

```cpp
camera_velodyne_translation.x = -camera_velodyne_point.z;
camera_velodyne_translation.y = camera_velodyne_point.x;
camera_velodyne_translation.z = camera_velodyne_point.y;

```

故真正的平移向量 x_real = y ; y_real = z ; z_real = - x 。以本文标定结果为例平移向量应该为：  
\[-9.7983495904432213e-02, 6.7093598780405939e-02, -1.1396690507528557e+00]

其余的几个函数跟一般标定结果相同。关于详细的投影操作可参考我的其他博客。 
 [https://blog.csdn.net/qq_22059843/article/details/103022451](https://blog.csdn.net/qq_22059843/article/details/103022451)
