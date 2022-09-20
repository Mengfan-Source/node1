# node1
# 12.虚拟机Ubuntu18.04安装ORBSLAM2

### 资源

###### Ubuntu18.04 + ROS melodic 安装使用 InterRealSenseD435i SDK2和RealSense-ROS以及查看相机内参（吐血整理，踩坑总结）

https://blog.csdn.net/m0_60355964/article/details/124006563

###### 高翔ORB-SLAM2稠密建图编译（添加实时彩色点云地图+保存点云地图）

https://blog.csdn.net/m0_60355964/article/details/124735742

###### D435i缺少对齐话题的解决

https://blog.csdn.net/YOULANSHENGMENG/article/details/125334427

##### 1.orbslam主页

https://github.com/raulmur/ORB_SLAM2

##### 2.安装Pangolin

选择v0.5版本

```
git clone --branch v0.5 https://github.com/stevenlovegrove/Pangolin.git
cd Pangolin
```

```
mkdir bulid 
cd build
cmake ..
sudo make -j
sudo make install
```

##### 3.安装opencv

```
git clone https://github.com/opencv/opencv.git
cd opencv
mkdir build
cd build
cmake ..
make -j2
sudo make install
```

##### 4.（不用安装，原系统中有eigen3的二进制安装版本）安装eigen3

```
git clone https://gitlab.com/libeigen/eigen.git
cd eigen
mkdir build
cd build
cmake ..
sudo make install

```

##### 5.编译orbslam2

```
git clone https://github.com/raulmur/ORB_SLAM2.git ORB_SLAM2
cd ORB_SLAM2
chmod 777 build.sh
./build.sh
```

![image-20220914113520264](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220914113520264.png)

##### 6.跑RGBD示例

```
./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUM1.yaml /home/xmf/orbslam_ws/database/rgbd_dataset_freiburg1_xyz /home/xmf/orbslam_ws/database/rgbd_dataset_freiburg1_xyz/associations.py
```

./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUM1.yaml /home/xmf/orbslam_ws/database/rgbd_dataset_freiburg1_xyz /home/xmf/orbslam_ws/database/rgbd_dataset_freiburg1_xyz/associations.txt

##### 7.与ros相关

```
export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:PATH/ORB_SLAM2/Examples/ROS
chmod +x build_ros.sh
./build_ros.sh
```

中间执行最后一步会报很多错误，-lboost_system 

在ORBSLAM2/Examples/ROS/ORBSLAM2下的Cmakelists.txt中添加一行，-lboost_system ，即：![image-20220914113417131](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220914113417131.png)

即可编译通过

##### 8.安装realsense驱动

https://github.com/IntelRealSense/realsense-ros

官方链接：

###### 步骤一：

- 注册服务器的公钥： 
  `sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key  F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key  F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE` 如果仍然无法检索公钥，请检查并指定代理设置： `export http_proxy="http://<proxy>:<port>"`
  ，然后重新运行命令。 请参阅以下 [链接 ](https://unix.stackexchange.com/questions/361213/unable-to-add-gpg-key-with-apt-key-behind-a-proxy)。 
- 将服务器添加到存储库列表中： 
  `sudo add-apt-repository "deb https://librealsense.intel.com/Debian/apt-repo $(lsb_release -cs) main" -u`
- 安装库（如果升级包，请参阅下面的部分）： 
  `sudo apt-get install librealsense2-dkms`
  `sudo apt-get install librealsense2-utils`
  以上两行将部署 librealsense2 udev 规则、构建和激活内核模块、运行时库以及可执行的演示和工具。 
- 可选择安装开发者和调试包： 
  `sudo apt-get install librealsense2-dev`
  `sudo apt-get install librealsense2-dbg`
  和 `dev`编译应用程序 **librealsense** 使用 `g++ -std=c++11 filename.cpp -lrealsense2`或您选择的 IDE。 

重新连接英特尔实感深度摄像头并运行： `realsense-viewer`验证安装。 

验证内核是否已更新： 
 `modinfo uvcvideo | grep "version:"`应包括 `realsense`细绳 

- ###### Step 2: Install Intel® RealSense™ ROS from Sources

  - Create a [catkin](http://wiki.ros.org/catkin#Installing_catkin) workspace *Ubuntu*

  ```
  mkdir -p ~/catkin_ws/src
  cd ~/catkin_ws/src/
  ```

  *Windows*

  ```
  mkdir c:\catkin_ws\src
  cd c:\catkin_ws\src
  ```

  - Clone the latest Intel® RealSense™ ROS from [here](https://github.com/intel-ros/realsense/releases) into 'catkin_ws/src/'

  ```
  git clone https://github.com/IntelRealSense/realsense-ros.git
  cd realsense-ros/
  git checkout `git tag | sort -V | grep -P "^2.\d+\.\d+" | tail -1`
  cd ..
  ```

  - Make sure all dependent packages are installed. You can check .travis.yml file for reference.
  - Specifically, make sure that the ros package *ddynamic_reconfigure* is installed. If *ddynamic_reconfigure* cannot be installed using APT or if you are using *Windows* you may clone it into your workspace 'catkin_ws/src/' from [here](https://github.com/pal-robotics/ddynamic_reconfigure/tree/kinetic-devel)

  ```
  catkin_init_workspace
  cd ..
  catkin_make clean
  
  这里出问题了，使用命令sudo apt-get install ros-melodic-ddynamic-reconfigure解决
  
  
  catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release
  catkin_make install
  ```

  *Ubuntu*

  ```
  echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
  source ~/.bashrc
  ```

  *Windows*

  ```
  devel\setup.bat
  ```

## 

###### Usage Instructions

###### Start the camera node

To start the camera node in ROS:

```
roslaunch realsense2_camera rs_camera.launch
```

This will stream all camera sensors and publish on the appropriate ROS topics.

Other stream resolutions and frame rates can optionally be provided as parameters to the 'rs_camera.launch' file.

### 高翔修改版

```
教程链接：https://blog.csdn.net/m0_60355964/article/details/124735742
装好之后测试命令：高翔修改版的mono_tum在bin下面
./bin/mono_tum Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml /home/xmf/orbslam_ws/database/rgbd_dataset_freiburg1_xyz
```

在编译和ros关联时，和第一次的原版ORBSLAM冲突

https://blog.csdn.net/jUst3Doit/article/details/113737364

在bashrc中将原版的删掉

export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:/home/xmf/ORB_SLAM2/Examples/ROS

添加export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:/home/xmf/ORBSLAM2_with_pointcloud_map/ORB_SLAM2_modified/Examples/ROS

并source ~/.bashrc

然后查看：echo $ROS_PACKAGE_PATH



##### 宝贝教程:按照这个安装高翔的修改版

##### 高翔orbslam_ORB SLAM 2 + 构建点云地图 复现

https://blog.csdn.net/weixin_39897392/article/details/111855993?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-111855993-blog-124735742.topnsimilarv1&spm=1001.2101.3001.4242.1&utm_relevant_index=3

安装完成之后测试运行

roscore

**xmf@ubuntu:~/ORBSLAM2_with_pointcloud_map/ORB_SLAM2_modified$**rosrun ORB_SLAM2 RGBD Vocabulary/ORBvoc.bin Examples/RGB-D/TUM1_ROS.yaml

**xmf@ubuntu:~/orbslam_ws/database$** rosbag play rgbd_dataset_freiburg1_xyz.bag /camera/rgb/image_color:=/camera/rgb/image_raw /camera/depth/image:=/camera/depth_registered/image_raw

官网下载下来数据集中话题包含：![image-20220916194435564](/C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20220916194435564.png)



d435i实际发布的话题![image-20220916194626139](/C:/Users/Lenovo/AppData/Roaming/Typora/typora-user-images/image-20220916194626139.png)

录制相机数据集

rosbag record /camera/color/image_raw /camera/aligned_depth_to_color/image_raw

播放数据集并且将话题修改为目标话题

rosbag play 2022-09-16-06-54-27.bag /camera/color/image_raw:=/camera/rgb/image_raw /camera/aligned_depth_to_color/image_raw:=/camera/depth_registered/image_raw

在录制话题时发现缺少一些话题，IMU数据和同步的深度数据，将这里改为true，即可生成同步的深度数据，并且在生成同步的深度

![image-20220916225116527](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220916225116527.png)









【JETSON-NANO】SD卡系统备份克隆
luoganttcc
于 2021-03-14 11:53:29 发布 1050
收藏 11
分类专栏： 机器视觉 jetson nano 实战
版权
机器视觉 同时被 2 个专栏收录
140 篇文章 8 订阅
订阅专栏
jetson nano 实战
14 篇文章 2 订阅
订阅专栏

添加链接描述

添加链接描述
1、连接SD卡到主机

    将装好系统的SD卡通过读卡器连接主机。通过命令要看SD卡：

sudo fdisk -l

    1

    会有很多内容，可以在插入SD卡前后分别执行该命令，这样通过对比不同之处就可以找到/dev/sdc是这个SD卡，看到的有数字的是这个卡的各个分区。

2、对SD卡模型进行备份

    我们使用的是dd命令，关于dd命令的详细说明参看另外一份博客,使用过程中要小心，避免原文件损坏。要说明的是，系统备份直接使用dd命令原SD卡存储多大，备份的文件就会有多大，所以要进行压缩备份；另外，对备份文件的恢复等其它操作要在同一台Host上进行操作。
    备份命令为：

sudo dd if=/dev/sdc conv=sync,noerror bs=8M | gzip -c > ~/nano.img.gz

    1

也可以同时输出日志

＃　sudo dd if=/dev/sdc conv=sync,noerror bs=120M status=progress  | gzip -c > ~/nano.img.gz

    1

3、等待30分钟
4、插入一张新的格式化好的sd 卡，复制系统

sudo gzip -dc /home/ledi/nano.img.gz | sudo dd of=/dev/sdc  bs=120M status=progress






