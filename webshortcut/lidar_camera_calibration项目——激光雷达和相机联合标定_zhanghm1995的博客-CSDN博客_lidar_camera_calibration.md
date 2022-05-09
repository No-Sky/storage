# lidar_camera_calibration项目——激光雷达和相机联合标定_zhanghm1995的博客-CSDN博客_lidar_camera_calibration
![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

作为无人驾驶中常用的传感器，**激光雷达和相机**都有自己特有的优势，然而它们也各有缺点和不足，现在主流的感知算法都向传感器融合方法靠拢，这就涉及到不同传感器之间的标定问题，本博文主要针对一个[开源项目](https://so.csdn.net/so/search?q=%E5%BC%80%E6%BA%90%E9%A1%B9%E7%9B%AE&spm=1001.2101.3001.7020)讲解激光雷达和相机之间的外参标定，即如何通过实验的方法得到激光雷达和相机之间的旋转平移矩阵。

> **项目地址**： [https://github.com/ankitdhall/lidar_camera_calibration](https://github.com/ankitdhall/lidar_camera_calibration)  
> **项目描述**：该功能包用来找到激光雷达和相机之间的旋转平移变换，该包使用 aruco_ros 和一个稍微修改的 aruco_mapping 包为依赖，lidar_camera_calibration/pointcloud_fusion 可以用来融合点云和两个双目相机的信息，两个双目相机都和激光雷达进行过外参标定。  
> 在使用该包之前，需要先做好相机内参标定，因为需要一些相机内参参数的一些信息。

**项目截图：** 

![](https://img-blog.csdnimg.cn/20190220160803133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5naG0xOTk1,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2019022016122285.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5naG0xOTk1,size_16,color_FFFFFF,t_70)

## lidar_camera_calibration ROS 包配置过程记录

#### 依赖配置：

1、先将整个[Github](https://so.csdn.net/so/search?q=Github&spm=1001.2101.3001.7020)包 clone 下来，放在已经建好的 ROS 工作空间下，clone 完成后生成文件夹 lidar_camera_calibration；  
2、将文件夹 lidar_camera_calibration 下的 dependencies 路径下的两个目录 aruco_mapping 和 aruco_ros 拷贝到 ROS 工作空间的 src 路径下；  
准备编译：由于各个包之间有依赖关系，必须按以下顺序安装  
3、先编译 aruco_ros 包

```
catkin_make -DCATKIN_WHITELIST_PACKAGES="aruco_ros"

```

4、再编译 lidar_camera_calibration 包

```bash
catkin_make -DCATKIN_WHITELIST_PACKAGES="lidar_camera_calibration"

```

此时可能会报错，缺少`velodyne_msgs`包，需要安装参考以下网站：  
[http://wiki.ros.org/velodyne/Tutorials/Getting Started with the HDL-32E](http://wiki.ros.org/velodyne/Tutorials/Getting%20Started%20with%20the%20HDL-32E)

#### 安装过程：

项目依赖 velodyne ROS 包，需要下载安装：

```
sudo apt-get install ros-kinetic-velodyne

```

5、再次编译`lidar_camera_calibration`包

```
catkin_make -DCATKIN_WHITELIST_PACKAGES="lidar_camera_calibration"

```

6、编译`aruco_mapping`包

```
catkin_make -DCATKIN_WHITELIST_PACKAGES="aruco_mapping"

```

## 运行过程

编译通过后，通过`find_transform.launch`文件启动所需节点和设置节点参数。  
在运行`launch`文件之前，需要修改`launch`文件中的部分内容：  
1、`launch`文件中默认将`ArUco mapping`节点注释掉了，可能需要启动该节点，并修改其中的重映射命令使得获取正确的相机图片话题：

```
 <remap from="/image_raw" to="/frontNear/left/image_raw"/>

```

修改相机标定文件路径参数（.ini 格式文件），aruco 标记的个数，标记尺寸（单位为米）  
\*\*注：\*\*图片话题消息类型为`sensor_msgs/Image`类型  
`aruco mapping`用来在图像中找到`aruco marker`，并发布 6DOF 的位姿.  
[http://wiki.ros.org/aruco_mapping](http://wiki.ros.org/aruco_mapping)  
2. `lidar_camera_calibration_rt`话题是由 aruco_mapping 包发送出来的，包含了利用`aruco marker`计算出来的旋转平移[矩阵](https://so.csdn.net/so/search?q=%E7%9F%A9%E9%98%B5&spm=1001.2101.3001.7020)数据  
启动标定节点前，需要保证 aruco 标记能够在相机画面内可见，并且需要使这些标记的 ID 号从左到右按升序排列，运行过程第一次需要手动标记`line-segments`  
**标记过程：**  
每块板上都有四个线段，需要从左到右依次标记每个板上的直线段；此处对于每个板上的直线标记是在该线段的周围绘制一个四边形，每次点击一个点，并按下键盘确认，得到四个点之后就得到了包围一根直线的一个四边形，按这个方法把一个板上的直线从左上按顺时针依次标记。  
标记完所有的直线段之后，就得到了最终的变换矩阵，中间值存储在`conf/points.txt`。  
`points.txt`文件

**输出：**  3x1 的平移矩阵和 3x3 的旋转矩阵，设置迭代多少次，就有多少个这样的矩阵生成。通过对 n 次迭代生成的平移矩阵求平均，即可得到最终的平移矩阵，通过对 n 个旋转矩阵转换成四元数，然后求平均，即可得到最终的旋转矩阵，从而得到最终的标定矩阵。

## 程序解析

**注：**  该部分由于自己时间有限，暂时还没有完全写完，有空继续更新。  
`lidar_camera_calibration/conf`目录包含了许多用来完成相机和雷达标定过程的配置文件

\*\*注意：\*\*其中的 cloud_intensity_threshold 用来滤除反射强度低于该值的点云，如果发现标定板上点云太少，可能需要修改该值试一下；  
`use_camera_info_topic`用来表示是否使用相机消息的话题来获得相机的标定参数，否则从该 config 文件中读取标定参数，默认从文件读取；  
相机投影矩阵对应的是`CameraInfo`中的矩阵 K，表示的是将相机坐标系下的三维点，对应到像素坐标系下的位置。

标定计算过程由`Find_RT.h`文件完成； 
 [https://blog.csdn.net/zhanghm1995/article/details/87802656](https://blog.csdn.net/zhanghm1995/article/details/87802656)
