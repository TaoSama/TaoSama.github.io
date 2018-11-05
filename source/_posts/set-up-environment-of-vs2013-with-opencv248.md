---
title: VS2013 + opencv2.4.8 环境搭建
categories:
  - Doing
  - Computer Vision
  - 
tags:
  - 
  - 
  - 
date: 2016-11-01 16:13:10
toc: true
---

### 前言
第一天来实习呀，宇哥让我搭建一波环境，来之前搞过一波$win+qt$的，过来发现这边并不是玩$linux$
而是$win10+vs$的节奏，然后我就知道要开始开(踩)心(坑)之旅了

### 开始搭建环境

#### 工具下载

* $vs\ 2013\ ultimate$安装，可以去[MSDN, I tell you](http://www.itellyou.cn/)下载
* $cmake$，直接[官网](http://www.cmake.org/)下载就好了，最新版本就好
* $opencv\ 2.4.8$，直接去官网下载源码就好

#### 编译
* $cmake\ gui$选择$opencv$源码根目录，再选择一个输出目录
  编译静态库就不打勾这2个， `BUILD_SHARED_LIBS`，`BUILD_WITH_STATIC_CRT`
  `QT` 方案就 `WITH_OPENGL` 和 `WITH_QT` 勾一下
* 点击$configure$，选择$Visual\ Studio\ 12\ 2013$就好，编译器就默认就行
* $configure$完之后点击$generate$，之后$open\ project$
* 打开$OpenCV$工程之后，分别在$Debug$和$Release$模式下，$CMakeTargets$下$ALL\_BUILD$和$INSTALL$先后构建一下

#### 依赖

* 新建一个控制台工程，选择空项目，新建 `main.cpp`，找一段官网代码
    ```cpp
    #include <opencv2/highgui/highgui.hpp>
    #include <iostream>
    
    using namespace cv;
    using namespace std;
    
    int main(int argc, char** argv)
    {
    	if (argc != 2)
    	{
    		cout << " Usage: display_image ImageToLoadAndDisplay" << endl;
    		return -1;
    	}
    
    	Mat image;
    	image = imread(argv[1], IMREAD_COLOR); // Read the file
    
    	if (!image.data) // Check for invalid input
    	{
    		cout << "Could not open or find the image" << std::endl;
    		return -1;
    	}
    
    	namedWindow("Display window", WINDOW_AUTOSIZE); // Create a window for display.
    	imshow("Display window", image); // Show our image inside it.
    
    	waitKey(0); // Wait for a keystroke in the window
    	return 0;
    }
    ```

* 有了上面这个 打开的工程就可以为所有工程添加依赖了
* $View\to Property\ Manager\to 工程名字\to Debug\ |\ Win32\to Microsoft.Cpp.Win32.user$
  上面这个位置找到之后，就可以添加包含目录和库目录了
  * $Common\ Properties\to VC++\ Directories$
  **包含目录**，编译后目录下的 `...\install\include`，`...\install\include\opencv`，`...\install\include\opencv2`三个目录添加就好
  **库目录**，静态库，编译后目录下的 `...\install\x86\vc12\staticlib`
  **可执行文件目录**，直接添加到环境变量里去，`...\install\x86\vc12\bin`
  * $Linker\to Input$
  **库文件依赖**，静态库依赖，库目录里的全部塞进去就行，$Debug$就添加 `*d.lib`版本的，$Release$添加 `*.lib`版本的

       ```cpp
       opencv_core248d.lib
       opencv_imgproc248d.lib
       opencv_highgui248d.lib
       opencv_ml248d.lib
       opencv_video248d.lib
       opencv_features2d248d.lib
       opencv_calib3d248d.lib
       opencv_objdetect248d.lib
       opencv_contrib248d.lib
       opencv_legacy248d.lib
       opencv_flann248d.lib
       libpngd.lib
       libtiffd.lib
       zlibd.lib
       IlmImfd.lib
       libjasperd.lib
       libjpegd.lib
       ```

### 各种坑点
* **静态库需要注意**，`highgui`会需要下面这一堆。。。。库文件依赖里加一下（**大坑**）
   [参考1](http://stackoverflow.com/questions/8098272/opencv-unresolved-external-symbols-other-libraries-needed)，[参考2](http://blog.csdn.net/u011636160/article/details/51702368)

    ```cpp
    comctl32.lib
    vfw32.lib
    user32.lib
    gdi32.lib
    winmm.lib
    uuid.lib
    winspool.lib
    wsock32.lib
    rpcrt4.lib
    odbc32.lib
    oleaut32.lib
    advapi32.lib
    comdlg32.lib
    ole32.lib
    ```
* ` error LNK2038: 检测到“RuntimeLibrary”的不匹配项:  值“MTd_StaticDebug”不匹配值“MDd_DynamicDebug” `
**解决方案：**
$在工程上右键\to 属性\to C/C++\to 代码生成\to 运行库$
有四个选项及含义分别如下：
多线程调试Dll (/MDd) 对应的是MD_DynamicDebug
多线程Dll (/MD) 对应的是MD_DynamicRelease
多线程(/MT) 对应的是MD_StaticRelease
多线程(/MTd)对应的是MD_StaticDebug
**根据错误提示修改即可**


### 后记
$windows$有毒，$vs$有毒。。