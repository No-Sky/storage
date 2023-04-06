# 计算机视觉（相机标定；内参；外参；畸变系数）_思妙想多多财-杰的博客-CSDN博客
**目录**

[一、预备知识](#t0)

[1、坐标系变换过程（相机成像过程）](#t1)

[（1）相机坐标系转换为图像坐标系（透视投影变换遵循的是针孔成像原理）](#t2)

[（2）齐次坐标的引入原因：（为什么引入齐次坐标？？？）](#t3)

[2、内参与外参矩阵的构成](#t4)

[3、畸变参数](#t5)

[二、相机标定](#t6)

[1、张正友标定法（光学标定）及其求解思路](#t7)

[ 2、求解内参矩阵与外参矩阵的思路：](#t8)

[（1）计算内参与外参矩阵的乘积M](#t9)

[（2）求解内参矩阵A](#t10)

[（3）求解外参矩阵(R1 R2 T)](#t11)

[三、参数优化](#t12)

[1、L-M算法](#t13)

[2、相机标定步骤](#t14)

[四、相关代码及其实验结果](#t15)

[1、相同角度的相机标定](#t16)

[（1）正面](#t17)

[内参矩阵：](#t18)

[畸变参数](#t19)

[旋转矩阵](#t20)

[ 平移矢量](#t21)

[ 反投影误差](#t22)

[ （2）侧面](#t23)

[内参矩阵](#t24)

[畸变参数](#t25)

[ 旋转矩阵](#t26)

[ 平移向量](#t27)

[反投影误差](#t28)

[ 2、不同角度的相机标定](#t29)

[内参矩阵](#t30)

[畸变参数](#t31)

[旋转矩阵](#t32)

[平移矩阵](#t33)

[反投影误差](#t34)

[6、总结](#t35)

* * *

1、坐标系变换过程（相机成像过程）
-----------------

相机成像过程涉及到四个坐标系的变换，变换关系如下：

（U,V,W）是世界坐标系，经过刚体变换（如：旋转、平移）后变为了[相机坐标系](https://so.csdn.net/so/search?q=%E7%9B%B8%E6%9C%BA%E5%9D%90%E6%A0%87%E7%B3%BB&spm=1001.2101.3001.7020)，再次经过透视投影转变为了图像坐标系，最后经仿射变换转换为了像素坐标系（u,v）。转换关系如下(Z是尺度因子)：

![](https://img-blog.csdnimg.cn/195b8ae10a69443883fc8198d0b359a4.png)

### （1）相机坐标系转换为图像坐标系（透视投影变换遵循的是针孔成像原理）

![](https://img-blog.csdnimg.cn/b095d55880f744989575d2538af5d701.png)

![](https://img-blog.csdnimg.cn/6e121dbcd1e144c19e539e47b72782f2.png)

### （2）[齐次坐标](https://so.csdn.net/so/search?q=%E9%BD%90%E6%AC%A1%E5%9D%90%E6%A0%87&spm=1001.2101.3001.7020)的引入原因：（[为什么引入齐次坐标？？？](https://www.jianshu.com/p/0cce4406d5ad "为什么引入齐次坐标？？？")）

总结如下：

**a. 升维---把图像的变换表示为矩阵相乘的形式，以进行统一计算**

图像的变换大多可以使用矩阵乘法来表示变换前后像素的映射关系，但是平移关系却只能使用矩阵加法表示，无法使用矩阵乘法表示。在表示图像变换时（如平移、旋转、缩放），矩阵涉及到乘法与加法，会使计算增加。

所以通过升维的方式来统一矩阵的形式，进而减少计算量。

**b. 升维---把无穷远的坐标表示为可运用计算的形式**

例子：如果笛卡尔坐标系下的点（3,4）移动到无穷远处，此时的坐标为（oo，oo）,使用齐次坐标就可以表示为（3,4,0）。

发现：使用齐次坐标，可以表示平行线在透视空间的无穷远处交于一点。在欧氏空间，这变得没有意义，所以欧式坐标不能表示。

齐次坐标转为欧式空间（3,4,5）=> (3/5,4/5) ---（6,8,10）=> (3/5,4/5)---（24,32,40）=> (3/5,4/5)

由此可以发现：齐次坐标具有规模不变性

2、内参与外参矩阵的构成
------------

内参矩阵：只与相机本身有关，取决于相机的内部参数。

外参矩阵：相机拍摄图片不同，相应的参数会发生变化，即随着世界坐标系与相机坐标系的相对位置而变。

在相机标定求参数中，共有11个参数变量。其中，相机的内部参数有5个：焦距，像主点坐标，畸变参数；相机的外部参数有6个：旋转，平移。

内参矩阵表示为

![](https://img-blog.csdnimg.cn/f93d83180a1549a39b5bd62910a6e73a.png)

 其中， f为像距，dX、dY分别表示X、Y方向上的一个像素在相机感光板上的物理长度，即一个像素在感光板上是多少毫米，u0，v0分别表示相机感光板中心在像素坐标系下的坐标，θ表示感光板的横边和纵边之间的角度（  90°表示无误差）。

外参矩阵表示为

![](https://img-blog.csdnimg.cn/6c40cc74f54342459e0fafd3f6d78d78.png)

  其中，R表示旋转矩阵，  T表示平移矢量。

3、畸变参数
------

畸变主要分为径向畸变和切向畸变。

**径向畸变**：沿着透镜半径方向分布的畸变；

产生原因：是由透镜质量引起的，光线在远离透镜中心的地方比靠近中心的地方更加弯曲。

径向畸变主要包括桶形畸变和枕形畸变两种。

![](https://img-blog.csdnimg.cn/e4336f057de145209572849c301c4df7.png)

**切向畸变：** 切向畸变是由于透镜本身与相机传感器平面（像平面）或图像平面不平行而产生的。

 切向畸变主要发生在相机传感器和镜头不平行的情况下；因为有夹角，所以光透过镜头传到图像传感器上时，成像位置发生了变化。

![](https://img-blog.csdnimg.cn/c8a563af8ab3457ba2d163e8068f7f26.png)

**畸变模型：** 

 径向畸变模型：

![](https://img-blog.csdnimg.cn/a4a95f735d994245847e1b4619941885.png)

 切向畸变模型：![](https://img-blog.csdnimg.cn/3ddb99c926724809a468cdaaa59a50b0.png)

 径向畸变+切向畸变模型：

![](https://img-blog.csdnimg.cn/08090dc8e87c44ee96e24f3a458d6427.png)

 畸变模型中（x',y'）为畸变后的归一化图像坐标，（x,y）为理想的无畸变的归一化的图像坐标。

 r为图像像素点到图像中心点的距离，即![](https://latex.codecogs.com/gif.latex?r%5E%7B%5E%7B2%7D%7D%3Dx%5E%7B2%7D&plus;y%5E%7B%5E%7B2%7D%7D)
  。

 其中，k1、k2、k3是径向畸变参数；p1、p2切向畸变参数。

在无畸变的标定中，假设内参矩阵为 K，那么经过畸变之后会在矩阵中增加一个畸变参数。

![](https://img-blog.csdnimg.cn/b2cdca25a3a048058a96df3e0cb9b8c2.png)
——发生径向畸变之后——![](https://img-blog.csdnimg.cn/69caee45d9834fdaa8a1e8e3b821a96d.png)

同步标定内部参数和外部参数，一般包括两种策略:

a. 光学标定: 利用已知的几何信息(如定长棋盘格)实现参数求解；

b. 自标定: 在静态场景中利用 structure from motion估算参数。

通过空间中已知坐标的(特征)点 (Xi,Yi,Zi)，以及它们在图像中的对应坐标 (ui,vi)，直接估算 11 个待求解的内部和外部参数。

1、张正友标定法（光学标定）及其求解思路
--------------------

 [参考：张正友相机标定法](https://zhuanlan.zhihu.com/p/94244568 "参考：张正友相机标定法")

张正友标定法利用棋盘格标定板图像，利用相应图像检测算法得到每个角点的像素坐标 （u,v）。

张正友标定法将世界坐标系固定于棋盘格上，则棋盘格上任一点的物理坐标W=0 ，由于标定板的世界坐标系是人为事先定义好的，标定板上每一个格子的大小是已知的，我们可以计算得到每一个角点在世界坐标系下的物理坐标(U,V,W=0)。

我们将利用这些信息：每一个角点的像素坐标(u,v) ，每一个角点在世界坐标系下的物理坐标(U,V,W=0)，来进行相机的标定，获得相机的内外参矩阵、畸变参数。

![](https://img-blog.csdnimg.cn/feaf77ba7c5e4e99bd8649a52b37aaf5.png)

 2、求解内参矩阵与外参矩阵的思路：
------------------

将世界坐标系固定于棋盘格上，则棋盘格上任一点的物理坐标 W=0，因此，原单点无畸变的成像模型可以化为下式。其中，(R1 R2 T)为旋转矩阵R的前两列。

![](https://img-blog.csdnimg.cn/b82e8fbe1084401db89c172e76dbe03e.png)

1\. 计算内参与外参矩阵的乘积M--> 2. 求解内参矩阵A--> 3. 求解外参矩阵(R1 R2 T)

### （1）计算内参与外参矩阵的乘积M

![](https://img-blog.csdnimg.cn/ea7ae586f0ac452fadf3d57324331634.png)

![](https://img-blog.csdnimg.cn/4e57a2da26ba4d31984f38b4533ec30d.png)

 其中![](https://latex.codecogs.com/gif.latex?x%3D%28u%2Cv%2C1%29%5E%7BT%7D)
，![](https://latex.codecogs.com/gif.latex?X%3D%28X%2CY%2CZ%2C1%29%5E%7BT%7D)
，M矩阵是齐次矩阵，有8个未知元素，每一个标定板上的角点，将提供两个约束方程。一张图片上只需4个角点即可求出矩阵M，一张图片上的角点多于4个时，可以使用最小二乘法求解最佳M。

### （2）求解内参矩阵A

利用M1=A(R1,R2,T)求解内擦矩阵A。

![](https://img-blog.csdnimg.cn/7851b1350ca543d6bab2a74b4fceeb85.png)

 R1,R2作为旋转矩阵的两列，具有单位正交性，满足以下公式：

**![](https://latex.codecogs.com/gif.latex?R_%7B1%7D%5E%7BT%7DR_%7B2%7D%3D0%2C%20R_%7B1%7D%5E%7BT%7DR_%7B1%7D%3DR_%7B2%7D%5E%7BT%7DR_%7B2%7D%3D1)
。** 

由![](https://latex.codecogs.com/gif.latex?R_%7B1%7D%3DA%5E%7B-1%7DM_%7B1%7D%20%2C%20R_%7B2%7D%3DA%5E%7B-1%7DM_%7B2%7D%20%2C)
，知

![](https://latex.codecogs.com/gif.latex?M_%7B1%7D%5E%7BT%7DA%5E%7B-T%7DA%5E%7B-1%7DM_%7B2%7D%5E%7BT%7D%3D0%2C)
，

![](https://latex.codecogs.com/gif.latex?M_%7B1%7D%5E%7BT%7DA%5E%7B-T%7DA%5E%7B-1%7DM_%7B1%7D%5E%7BT%7D%3DM_%7B2%7D%5E%7BT%7DA%5E%7B-T%7DA%5E%7B-1%7DM_%7B2%7D%5E%7BT%7D%3D1)
。

记![](https://latex.codecogs.com/gif.latex?A%5E%7B-T%7DA%5E%7B-1%7D%3DB)
，则

![](https://img-blog.csdnimg.cn/972d1c6b637a4953872a09a3e3b0e1bc.png)

 其中，B为对称阵。

**![](https://latex.codecogs.com/gif.latex?M_%7B1%7D%5E%7BT%7DBM_%7B2%7D%5E%7BT%7D%3D0)**

**![](https://latex.codecogs.com/gif.latex?M_%7B1%7D%5E%7BT%7DBM_%7B1%7D%5E%7BT%7D%3DM_%7B2%7D%5E%7BT%7DBM_%7B2%7D%5E%7BT%7D%3D1)**

可记为![](https://latex.codecogs.com/gif.latex?M_%7Bi%7D%5E%7BT%7DBM_%7Bj%7D%5E%7BT%7D%3Dv_%7Bij%7Db)
，其中![](https://latex.codecogs.com/gif.latex?b%3D%5BB_%7B_%7B11%7D%7D%2C%20B_%7B_%7B12%7D%7D%2CB_%7B_%7B22%7D%7D%2CB_%7B_%7B13%7D%7D%2CB_%7B_%7B23%7D%7D%2CB_%7B_%7B33%7D%7D%20%5D)
。

![](https://img-blog.csdnimg.cn/27750da6af654a96aec2fe7ab2690f91.png)
    ![](https://img-blog.csdnimg.cn/1259954b1cbf4f9db72e4caa9c702b68.png)

每张标定板图片可以提供一个vb=0的约束关系，该约束关系含有两个约束方程。但是，向量b有6个未知元素。取3张标定板照片即可求解向量b 。当标定板图片数大于3时，用最小二乘法拟合最佳的向量 b，并得到矩阵B ；进而求出内参矩阵A。

![](https://img-blog.csdnimg.cn/1f6fd5731674409884106b17daa2828d.png)

![](https://img-blog.csdnimg.cn/6497cef90361443c990d44ff21fdea8d.png)

 **![](https://latex.codecogs.com/gif.latex?v_%7B0%7D%3D%5Cfrac%7BB_%7B12%7DB_%7B13%7D-B_%7B11%7DB_%7B23%7D%7D%7BB_%7B11%7DB_%7B22%7D-B_%7B12%7D%5E%7B2%7D%7D)**

**![](https://latex.codecogs.com/gif.latex?%5Calpha%20%3D%5Csqrt%7B%5Cfrac%7B1%7D%7BB_%7B11%7D%7D)**

**![](https://latex.codecogs.com/gif.latex?%5Cbeta%20%3D%5Csqrt%7B%5Cfrac%7BB_%7B11%7D%7D%7BB_%7B11%7DB_%7B22%7D-B_%7B12%7D%5E%7B2%7D%7D%7D)**

**![](https://latex.codecogs.com/gif.latex?%5Cgamma%20%3D-%7BB_%7B12%7D%5Calpha%20%5E%7B2%7D%7D%5Cbeta)**

**![](https://latex.codecogs.com/gif.latex?u_%7B0%7D%3D%5Cfrac%7B%5Cgamma%20v_%7B0%7D%7D%7B%5Cbeta%20%7D-B_%7B13%7D%5Calpha%20%5E%7B2%7D)**

### （3）求解外参矩阵(R1 R2 T)

对于不同的图片，标定板和相机的位置关系已经改变，此时每一张图片对应的外参矩阵都是不同的。

由![](https://latex.codecogs.com/gif.latex?H%3D%28R_%7B1%7D%2CR_%7B2%7D%2CT%29)
 知外参矩阵可表示为：

 ![](https://latex.codecogs.com/gif.latex?%28R_%7B1%7D%2CR_%7B2%7D%2CT%29%3DA%5E%7B-1%7DH)

即可求出每一张图片的外参矩阵![](https://latex.codecogs.com/gif.latex?%28R_%7B1%7D%2CR_%7B2%7D%2CT%29)
。

完整的外参矩阵中旋转矩阵R有三列，其中通过计算R3=R1×R2，即可得到完整的外参矩阵。

我们的总逻辑是，在进行内参矩阵和外参矩阵的求解的时候，我们假设不存在畸变；在进行畸变系数的求解的时候，假设求得的内参矩阵和外参矩阵是无误差的。最后，我们再通过L-M算法对于参数进行迭代优化。（张正友相机标定法仅考虑了畸变模型中影响较大的径向畸变）

1、L-M算法
-------

L-M方法全称Levenberg-Marquardt方法，是非线性回归中回归参数最小二乘估计的一种估计方法。这种方法是把最速下降法和线性化方法(泰勒级数)加以综合的一种方法。因为最速下降法适用于迭代的开始阶段参数估计值远离最优值的情况，而线性化方法，即高斯牛顿法适用于迭代的后期，参数估计值接近最优值的范围内。两种方法结合起来可以较快地找到最优值  。

LM算法的迭代式：

![](https://img-blog.csdnimg.cn/04843a59681f49a985e8af678674182b.png)

由上式知：L-M算法是高斯牛顿法与梯度下降法的结合：当u很小时，矩阵J接近矩阵G，其相当于高斯-牛顿法，此时迭代收敛速度快，当u很大时，其相当于梯度下降法，此时迭代收敛速度慢。因此LM算法即具有高斯-牛顿法收敛速度快、不容易陷入局部极值的优点，也具有梯度下降法稳定逼近最优解的特点。

在LM算法的迭代过程中，需要根据实际情况改变u的大小来调整步长：

(1) 如果当前轮迭代的目标函数值大于上轮迭代的目标函数值，即![](https://latex.codecogs.com/gif.latex?f_%7Bk&plus;1%7D%3Ef_%7Bk%7D)
，说明当前逼近方向出现偏差，导致跳过了最优点，需要通过增大u值来减小步长。

(2) 如果当前轮迭代的目标函数值小于上轮迭代的目标函数值，即![](https://latex.codecogs.com/gif.latex?f_%7Bk&plus;1%7D%3Cf_%7Bk%7D)
，说明当前步长合适，可以通过减小u值来增大步长，加快收敛速度。

2、相机标定步骤
--------

1\. 打印一张棋盘格A4纸张（黑白间距已知），并贴在一个平板上；

2\. 针对棋盘格拍摄若干张图片；

3\. 在图片中检测特征点（Harris角点）；

4\. 根据角点位置信息及图像中的坐标，求解内参矩阵与外参矩阵的积M ;

5\. 利用B矩阵计算出5个内部参数得内部矩阵A，以及 6个外部参数 ;

6. 求解畸变参数；

7\. 设计优化目标并实现参数的优化。

引用代码：[张正友相机标定法](https://github.com/1368069096/Calibration_ZhangZhengyou_Method "张正友相机标定法")

```null
lib(inter_corner_shape, size_per_grid, img_dir, img_type):# criteria: only for subpix calibration, which is not used here.# criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 30, 0.001)# cp_int: corner point in int form, save the coordinate of corner points in world space in 'int' form# like (0,0,0), (1,0,0), (2,0,0) ....,(10,7,0)._int = np.zeros((w * h, 3), np.float32) #返回来一个给定形状和类型的用0填充的数组  54行3列_int[:, :2] = np.mgrid[0:w, 0:h].T.reshape(-1, 2) #  三维网格坐标划分  行2列# cp_world: corner point in world space, save the coordinate of corner points in world space._world = cp_int * size_per_gridj_points = []  # the points in world spaceg_points = []  # the points in image space (relevant to obj_points)ages = glob.glob(img_dir + os.sep + '**.' + img_type)  gray_img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # find the corners, cp_img: corner points in pixel space.  ret, cp_img = cv2.findChessboardCorners(gray_img, (w, h), None)      # cv2.cornerSubPix(gray_img,cp_img,(11,11),(-1,-1),criteria)      obj_points.append(cp_world)      img_points.append(cp_img)      cv2.drawChessboardCorners(img, (w, h), cp_img, ret)      cv2.namedWindow('FoundCorners', cv2.WINDOW_NORMAL)      cv2.resizeWindow("FoundCorners", 600, 600)  # 设置窗口大小      cv2.imshow('FoundCorners', img)t, mat_inter, coff_dis, v_rot, v_trans = cv2.calibrateCamera(obj_points, img_points, gray_img.shape[::-1], None,int(("internal matrix:\n"), mat_inter)# in the form of (k_1,k_2,p_1,p_2,k_3)int(("------------------------------------------------------------------"))int(("distortion cofficients:\n"), coff_dis) #畸变系数k1,k2,k3径向畸变系数, p1,p2是切向畸变系数。int(("------------------------------------------------------------------"))int(("rotation vectors:\n"), v_rot)int(("------------------------------------------------------------------"))int(("translation vectors:\n"), v_trans)int(("------------------------------------------------------------------"))# calculate the error of reproject# 通过反投影误差，我们可以来评估结果的好坏。越接近0，说明结果越理想。# 通过之前计算的内参数矩阵、畸变系数、旋转矩阵和平移向量，使用cv2.projectPoints()计算三维点到二维图像的投影，# 然后计算反投影得到的点与图像上检测到的点的误差，最后计算一个对于所有标定图像的平均误差，这个值就是反投影误差r i in range(len(obj_points)):  img_points_repro, _ = cv2.projectPoints(obj_points[i], v_rot[i], v_trans[i], mat_inter, coff_dis)  error = cv2.norm(img_points[i], img_points_repro, cv2.NORM_L2) / len(img_points_repro)int(("Average Error of Reproject: "), total_error / len(obj_points))distortion(inter_corner_shape, img_dir, img_type, save_dir, mat_inter, coff_dis):ages = glob.glob(img_dir + os.sep + '**.' + img_type)  img_name = fname.split(os.sep)[-1]  newcameramtx, roi = cv2.getOptimalNewCameraMatrix(mat_inter, coff_dis, (w, h), 0, (w, h))  # 自由比例参数  dst = cv2.undistort(img, mat_inter, coff_dis, None, newcameramtx)  # dst = dst[y:y+h, x:x+w]  cv2.imwrite(save_dir + os.sep + img_name, dst)int('Dedistorted images have been saved to: %s successfully.' % save_dir)ter_corner_shape = (9, 6)t_inter, coff_dis = calib(inter_corner_shape, size_per_grid, img_dir, img_type)# dedistort and save the dedistortion result.ve_dir = ".\\pic\\photo5-ch" (not os.path.exists(save_dir)):distortion(inter_corner_shape, img_dir, img_type, save_dir, mat_inter, coff_dis)
```

1、相同角度的相机标定
-----------

### （1）正面

![](https://img-blog.csdnimg.cn/3a0ed8aa77c04f0ba14638ff76faa7d5.png)

 其中： 5张图片（1-5.jpg）10张图片（1-10.jpg）15张图片（1-15.jpg） 

### 内参矩阵：

**5张图片**

![](https://img-blog.csdnimg.cn/e64e310c0768488dae6a89c57c7eaca3.png)

 **10张图片**

![](https://img-blog.csdnimg.cn/0745e6230ee94faeb8264079efce8f3c.png)

**15张图片**

![](https://img-blog.csdnimg.cn/c27132d0a058492ebb548b37c9879aa7.png)

### 畸变参数

**5张图片**

![](https://img-blog.csdnimg.cn/9b0da8ec3fd245d995a90932290ca0cb.png)

 **10张图片**

![](https://img-blog.csdnimg.cn/7e816aef076c4994bfb04f6e7a222596.png)

**15张图片**

![](https://img-blog.csdnimg.cn/afda7d3918bb47bdb800299613124383.png)

### 旋转矩阵

**5张图片**

![](https://img-blog.csdnimg.cn/4e6f996dae0a4e229ae9261767ed6e7e.png)

 **10张图片**

![](https://img-blog.csdnimg.cn/30208fd31f7f4d248431e496b3073c8e.png)

 **15张图片**

![](https://img-blog.csdnimg.cn/72ea727933484483bfbcffa1cea8f79a.png)
   ![](https://img-blog.csdnimg.cn/abf78f69b3f34f6cabdcccc8ad424acd.png)

###  平移矢量

**5张图片**

![](https://img-blog.csdnimg.cn/193f4bb9b6a34705aca84d7a8bdbe421.png)

 **10张图片**

![](https://img-blog.csdnimg.cn/3c1e95e1964c466198fc721eb5781dce.png)

 **15张图片**

![](https://img-blog.csdnimg.cn/f1cf79b751954ed1887a5e9c50e814dd.png)
![](https://img-blog.csdnimg.cn/933c2aeefaae42e98e9e7e077d01635a.png)

###  反投影误差

**5张图片**

![](https://img-blog.csdnimg.cn/dc1ca306f2d048f2b7044e6fada73a58.png)

 **10张图片**

![](https://img-blog.csdnimg.cn/37cb33d9b9e94178bdcd52ce6bfaad80.png)

 **15张图片**

![](https://img-blog.csdnimg.cn/a5c94a59ed694307b4014a6ffab1d0ee.png)

###  （2）侧面

![](https://img-blog.csdnimg.cn/1a770bf3313144cf9174287ee9b45a2b.png)

 其中： 5张图片（1-5.jpg）10张图片（1-10.jpg）15张图片（1-15.jpg）

### 内参矩阵

**5张图片**![](https://img-blog.csdnimg.cn/707275d48728440084c78b36fb6f050e.png)

 **10张图片**![](https://img-blog.csdnimg.cn/d2d21fc427504e99a6f04f66d5f5ab29.png)

**15张图片**![](https://img-blog.csdnimg.cn/d4a65919af1749b29a061948f760f08f.png)

### 畸变参数

**5张图片**

![](https://img-blog.csdnimg.cn/cc7a6698d29149b09de8a4815e7cd8d4.png)

 **10张图片**![](https://img-blog.csdnimg.cn/d1f30afd49a646c4bb88ad69396c894e.png)

**15张图片**![](https://img-blog.csdnimg.cn/0ca935b5d00d40edb995e540ee036823.png)

###  旋转矩阵

**5张图片**

![](https://img-blog.csdnimg.cn/33e0e02dd6e94dc499b2a18b67f2e867.png)

 **10张图片**

![](https://img-blog.csdnimg.cn/2dffdde5b0424ed7b10189d45f30960e.png)

  **15张图片**

![](https://img-blog.csdnimg.cn/d9b93b688d2342469f4f427f560d063c.png)
  ![](https://img-blog.csdnimg.cn/95ec86bb15484b02bd6fe6440c9506df.png)

###   平移向量

**5张图片**

![](https://img-blog.csdnimg.cn/4df37de3d29f4caaaaea83e6c2f62abb.png)

**10张图片**

![](https://img-blog.csdnimg.cn/53610d8e770548a19f16a53ca7b21f56.png)

**15张图片**

![](https://img-blog.csdnimg.cn/7c0ea18977804e7a96f0f005b064789d.png)
   ![](https://img-blog.csdnimg.cn/45acffa465254dfca8be28258108119f.png)

###  反投影误差

**5张图片**![](https://img-blog.csdnimg.cn/20eef3fdcc7d4bcdb33793542be68051.png)

 **10张图片**![](https://img-blog.csdnimg.cn/cdc5c273b23f41ea93e99807c6313195.png)

**15张图片**

![](https://img-blog.csdnimg.cn/50336bdb06b44ae59dc7721f59507620.png)

 2、不同角度的相机标定
------------

![](https://img-blog.csdnimg.cn/1b0f66c00d0f41d78719b92e9838de4a.png)

 其中： 5张图片（1-5.jpg）10张图片（1-10.jpg）15张图片（1-15.jpg） 20张图片(1-20.jpg)

### 内参矩阵

5张图片

![](https://img-blog.csdnimg.cn/08ca1b1c70a544838085c02defe0d718.png)

**10张图片**

![](https://img-blog.csdnimg.cn/8bf94804168440a4b21227fd77e9cef5.png)

**15张图片**

![](https://img-blog.csdnimg.cn/762074b865d846d29795b51a8524354e.png)

**20张图片**

![](https://img-blog.csdnimg.cn/da5efbd88fbc45edbb1df6fb83a3e1b4.png)

### 畸变参数

**5张图片**

![](https://img-blog.csdnimg.cn/19fde849d5564db497e9d05c71b1b83f.png)

**10张图片**

![](https://img-blog.csdnimg.cn/b2fb0a3b1f1b49138626f24985638178.png)

**15张图片**

![](https://img-blog.csdnimg.cn/80912fcc9cce403f83dff5ab41039857.png)

**20张图片**

![](https://img-blog.csdnimg.cn/30264c021ebd435ba913d1be0c226e4c.png)

### **旋转矩阵**

**5张图片**

![](https://img-blog.csdnimg.cn/7669a60149844605b94e34bd829a4bfb.png)

**10张图片**

![](https://img-blog.csdnimg.cn/fe00819d218d4358975e782ea1696bb1.png)

**15张图片**

![](https://img-blog.csdnimg.cn/fe8c8b836be247bea822b272794f500d.png)
![](https://img-blog.csdnimg.cn/96c5d3bbac4c451c8c1058522c85e553.png)

**20张图片**

![](https://img-blog.csdnimg.cn/d60d91bfc8ab4e5cbb24a071b511cf72.png)
 ![](https://img-blog.csdnimg.cn/e9c816c49e66411895de1c0e4d6cd7b9.png)

### 平移矩阵

**5张图片**

![](https://img-blog.csdnimg.cn/ef43ae82da0942d39bc981dcfe35fcca.png)

**10张图片**

![](https://img-blog.csdnimg.cn/3abf23457c0344cda82cba5d6189f0da.png)

**15张图片**

![](https://img-blog.csdnimg.cn/05bb2d9af6ba4439a65ef618e8771c1d.png)
 ![](https://img-blog.csdnimg.cn/dbafb25bc5d3407781b1ae83d92ab93d.png)

**20张图片**

![](https://img-blog.csdnimg.cn/149469c50eb94d69aaf68ed32a3b41b8.png)
 ![](https://img-blog.csdnimg.cn/23a69360d48d4a7cac0329938ea564f4.png)

### 反投影误差

**5张图片**

![](https://img-blog.csdnimg.cn/6e643d3b53f14125814a9f5f5b794fb4.png)

**10张图片**

![](https://img-blog.csdnimg.cn/61b60797300040079aa1f21ea0cf2487.png)

**15张图片**

![](https://img-blog.csdnimg.cn/cccf52e9774e4b9c88a0c6517225be06.png)

**20张图片**

![](https://img-blog.csdnimg.cn/5ca6c21065f04e46b2d62318b6bb4a25.png)

6、总结
----

对于不同的图片，内参矩阵A为定值；对于同一张图片，内参矩阵A，外参矩阵(R1,R2,T)为定值；对于同一张图片上的单点，内参矩阵A，外参矩阵 (R1,R2,T)，尺度因子Z为定值。

对于同一个相机，相机的内参矩阵取决于相机的内部参数，无论标定板和相机的位置关系是怎么样的，相机的内参矩阵不变。

外参矩阵反映的是标定板和相机的位置关系。对于不同的图片，标定板和相机位置的关系有变化，所以每张图片对应的外参矩阵都是不同的。

通过反投影误差，我们可以来评估结果的好坏。越接近0，说明结果越理想。

我使用的角点形状为（9,6），角点之间的距离为0.026cm。

经过对不同拍摄角度的不同数量的实验对比，知：

1、经过相同角度的拍摄知：

（1）对于不同数量棋盘格，内参矩阵略有变化。

（2）畸变系数包含径向畸变与切向畸变，系数会随着棋盘格的数量发生变化。

（3）旋转矩阵、平移矢量针对不同的图片具有在不同数量的实验中的值略有变化。

正面10张图片中变化较大，可能时因为拍摄角度有晃动的原因。

（4）反投影误差随着我们棋盘格的数量变化不大。

2、经过不同角度的拍摄知：

（1）对于不同数量棋盘格，内参矩阵变化明显。

（2）畸变系数包含径向畸变与切向畸变，系数会随着棋盘格的不同数量的实验有变化。

（3）旋转矩阵、平移矢量针对不同的图片具有不同的值，相同的照片在不同数量的实验中，值的差异较大。

（4）反投影误差随着我们棋盘格的数量会逐渐增大。

通过反投影误差，我们可以来评估结果的好坏。越接近0，说明结果越理想。

3、由拍摄角度差异知：

无论拍摄角度、还是实验所使用的棋盘格的数量而言，内参矩阵，外参矩阵，畸变参数，反投影误差都有变化。即：每一次标定的结果都会发生变化。

每次标定的结果不同，可能的来源有这么几个:  
a. 计算的内外参数的初值不同，可能因为相机标定过程中，使用迭代优化时没有收敛到最小值，只是收敛到了局部最小值。  
b. 标定获得数据不稳定，标定板的大小不合适，这个影响也很大。