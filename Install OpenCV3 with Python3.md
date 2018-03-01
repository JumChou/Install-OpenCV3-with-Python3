# Install OpenCV3 with Python3.6

### 1. Easy Way
Mac通过Homebrew来安装OpenCV3的release包，我没有通过这种方式安装，但以optional为目的记录一下。
安装OpenCV3，指定python3支持、contribute包支持：
```shell
$ brew install opencv3 --with-python3 --with-contrib
```
安装完后，需注意python调用cv2的so文件路径是否需要在site-package目录中添加so文件的软链接，如：
```shell
$ cd my_tf/lib/python3.6/site-packages  
$ ln -s /usr/local/Cellar/opencv3/3.1.0_4/lib/python3.6/site-packages/cv2.cpython-36m-darwin.so cv2.so
```
  ​
### 2. Complete Way
  #### 1. 准备工作
  + **git**安装
      https://git-scm.com/download/
  + **Homebrew**
      https://brew.sh/
  + **CMake**命令
      https://cmake.org/download/
      shell中调用还需要将cmake添加到环境中：
      ```shell
      $ sudo "/Applications/CMake.app/Contents/bin/cmake-gui" --install
      ```
  + **Python3.6** + **virtualenv**虚拟环境 + **numpy**库
    本文基于虚拟环境目录**env3**，pip安装numpy至**env3**环境中。

    ​

  #### 2.下载OpenCV
  OpenCV官方提供源码主要下载两个仓库：
  + **opencv.git** - OpenCV核心库

  + **opencv_contrib.git** - OpenCV扩展库

    ​

  下载**opencv.git**仓库至用户根目录，并checkout版本：
  ```shell
  $ cd ~
  $ git clone https://github.com/Itseez/opencv.git
  $ cd opencv
  $ git checkout 3.4.0
  ```
  下载**opencv_contrib.git**仓库至用户根目录，并checkout版本：
  ```shell
  $ cd ~
  $ git clone https://github.com/Itseez/opencv_contrib
  $ cd opencv_contrib
  $ git checkout 3.4.0
  ```
  git命令使用Shadowsocks：
  ```shell
  $ git clone https://github.com/opencv/opencv.git --config http.proxy=localhost:8123
  $ git clone https://github.com/opencv/opencv_contrib.git --config http.proxy=localhost:8123
  ```
  
  ​
  
  #### 3. 编译OpenCV
  进入OpenCV路径中创建**build**目录并进入，准备编译：
  ```shell
  $ cd ~/opencv
  $ mkdir build
  $ cd build
  ```
  build目录中执行cmake编译命令：
  ```shell
  cmake -D CMAKE_BUILD_TYPE=RELEASE \
  -D CMAKE_INSTALL_PREFIX=/usr/local \
  -D PYTHON_DEFAULT_EXECUTABLE=~/Documents/[ML]/project/env3/bin/python \
  -D PYTHON3_EXECUTABLE=~/Documents/[ML]/project/env3/bin/python \
  -D PYTHON3_PACKAGES_PATH=~/Documents/[ML]/project/env3/lib/python3.6/site-packages \
  -D PYTHON3_LIBRARY=/Library/Frameworks/Python.framework/Versions/3.6/lib/libpython3.6m.dylib \
  -D PYTHON3_INCLUDE_DIR=/Library/Frameworks/Python.framework/Versions/3.6/include/python3.6m \
  -D PYTHON3_NUMPY_INCLUDE_DIRS=~/Documents/[ML]/project/env3/lib/python3.6/site-packages/numpy/core/include \
  -D INSTALL_C_EXAMPLES=OFF \
  -D INSTALL_PYTHON_EXAMPLES=ON \
  -D BUILD_EXAMPLES=ON \
  -D BUILD_opencv_python2=OFF \
  -D BUILD_opencv_python3=ON \
  -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules ..
  ```
  上面命令中注意几点：
  * PYTHON_DEFAULT_EXECUTABLE指定了默认解释器为Python3，否则编译时会默认寻找Mac系统自带的Python2.7解释器。
  * BUILD_opencv_python2、BUILD_opencv_python3指定了只编译Python3版本。
  * 需认真对照Python3虚拟环境env3路径、其他路径。
    我的env3路径：~/Documents/\[ML\]/project/env3/
    Python3路径：/Library/Frameworks/Python.framework/Versions/3.6/

  cmake执行完成后的输出信息：
  Python部分信息
  ```shell
  --   Python 3:
  --     Interpreter:                 /Users/zhoujunhao/Documents/[ML]/project/env3/bin/python (ver 3.6.2)
  --     Libraries:                   /Library/Frameworks/Python.framework/Versions/3.6/lib/libpython3.6m.dylib (ver 3.6.2)
  --     numpy:                       /Users/zhoujunhao/Documents/[ML]/project/env3/lib/python3.6/site-packages/numpy/core/include (ver 1.14.0)
  --     packages path:               /Users/zhoujunhao/Documents/[ML]/project/env3/lib/python3.6/site-packages
  -- 
  --   Python (for build):            /Users/zhoujunhao/Documents/[ML]/project/env3/bin/python
  ```
  完成部分信息
  ```shell
  -- -----------------------------------------------------------------
  -- 
  -- Configuring done
  -- Generating done
  -- Build files have been written to: /Users/jum/opencv/build
  ```
  接下来执行本地编译（多核编译，**4**决定核心数量）：
  ```shell
  $ make -j4
  ```

  ​

  #### 4. OpenCV安装
  如果在前面步骤成功编译，就可以开始安装了：
  ```shell
  $ make install
  ```
  如果提示权限问题则执行：
  ```shell
  $ sudo make install
  ```
  一切顺利的话，OpenCV我们就安装好了~

​

### 3. 测试OpenCV是否安装成功
查看虚拟环境env3中是否存在cv的so文件：
```shell
$ pwd
/path/to/your/virtualenv/env3
$ cd lib/python3.6/site-packages
$ ls -l|grep cv
-rwxr-xr-x   1 jum  staff  5535528  1 10 17:08 cv2.cpython-36m-darwin.so
```
python命令行中测试导入opencv：
```shell
$ pwd
/path/to/your/virtualenv/env3
$ source bin/activate
(env3)$ python
Python 3.6.2 (v3.6.2:5fd33b5926, Jul 16 2017, 20:11:06) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.4.0'
```

​

### 4. 卸载OpenCV
编译安装后卸载，build目录中执行：
```shell
$ sudo make uninstall
```
Homebrew安装后卸载：
```shell
$ brew uninstall opencv3
```

​

### 5. Ubuntu安装区别
apt-get更新：
```shell
$ sudo apt-get update
$ sudo apt-get upgrade
```
支持工具安装：
```shell
$ sudo apt-get install cmake git
$ sudo apt-get install build-essential
$ sudo apt-get install libgtk2.0-dev libavcodec-dev libavformat-dev libswscale-dev
$ sudo apt-get install libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
$ sudo apt-get install libv4l-dev
```
CMake命令（虚拟环境）：
```shell
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D PYTHON_DEFAULT_EXECUTABLE=~/EnvironmentAbout/python/py36env/bin/python \
-D PYTHON3_EXECUTABLE=~/EnvironmentAbout/python/py36env/bin/python \
-D PYTHON3_PACKAGES_PATH=~/EnvironmentAbout/python/py36env/lib/python3.6/site-packages \
-D PYTHON3_NUMPY_INCLUDE_DIRS=~/EnvironmentAbout/python/py36env/lib/python3.6/site-packages/numpy/core/include \
-D PYTHON_INCLUDE_DIR=/usr/include/python3.6 \
-D PYTHON_INCLUDE_DIR2=/usr/include/x86_64-linux-gnu/python3.6m \
-D PYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.6m.so \
-D BUILD_EXAMPLES=ON \
-D INSTALL_C_EXAMPLES=OFF \
-D INSTALL_PYTHON_EXAMPLES=ON \
-D OPENCV_EXTRA_MODULES_PATH=~/EnvironmentAbout/opencv_src/opencv_contrib/modules ..
```
CMake命令：
```shell
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D PYTHON_DEFAULT_EXECUTABLE=/usr/local/bin/python3 \
-D PYTHON3_EXECUTABLE=/usr/local/bin/python3 \
-D PYTHON3_PACKAGES_PATH=/usr/local/lib/python3.6/site-packages \
-D BUILD_EXAMPLES=ON \
-D INSTALL_C_EXAMPLES=OFF \
-D INSTALL_PYTHON_EXAMPLES=ON \
-D OPENCV_EXTRA_MODULES_PATH=/home/shasha/opencv_src/opencv_contrib/modules ..
```
CMake执行可能下载失败第三方binary（如ippicv）的github地址：
https://github.com/opencv/opencv_3rdparty/branches/all
本地下载ippicv上传至opencv目录，修改文件名前缀添加文件hash(md5)值，放置指定cache路径：
```shell
scp ~/Desktop/ippicv_2017u3_lnx_intel64_general_20170822.tgz ml@mlserver.local:~/EnvironmentAbout/opencv_src/opencv/

ipp_file=ippicv_2017u3_lnx_intel64_general_20170822.tgz &&
ipp_hash=$(md5sum ./$ipp_file | cut -d" " -f1) &&
ipp_dir=.cache/ippicv &&
mkdir -p $ipp_dir &&
cp ./$ipp_file $ipp_dir/$ipp_hash-$ipp_file
```

