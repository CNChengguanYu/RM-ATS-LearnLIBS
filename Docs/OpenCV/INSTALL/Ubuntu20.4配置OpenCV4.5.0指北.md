## Ubuntu20.4配置OpenCV4.5.0指北

安装前置环境

```
sudo apt-get install build-essential    
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev 
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev

sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
sudo apt update
sudo apt install libjasper1 libjasper-dev
```

请确定前置环境没有配置错误，如果出错可能导致后面的安装或者使用问题

如果部分软件无法下载，请尝试更换下载源

---

### 开始安装

1.解压Github上下载来的OpenCV4.5.0到指定目录(根据你想存放的地方决定)

2.解压OpenCV_Contrib_4.5.0到OpenCV4.5.0的目录<u>**内部**</u>

3.在OpenCV文件夹<u>内部</u>创建build文件夹

此时文件树应该为	OpenCV4.5.0── ~ 

​																└  build

​																└	OpenCV_Contrib_4.5.0

4.进入build文件夹	(请根据实际情况变更)

​	`cd ~当前文件路径~/OpenCV4.5.0(%OpenCV文件夹名称%)/build/`



5.执行 cmake 			(请根据实际情况变更)

```
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_GTK=ON -D 	OPENCV_GENERATE_PKGCONFIG=YES -D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib4.5.0(%opencv_contrib的文件夹名称%)/modules ..
```

如果环境配置不成功,则会在这里报错，请根据报错尝试配置环境

如果通过，则会显示`-- configuring done`,`-- Build file hava been written to :/~你的路径~/opencv4.5.0/build`之类的提示，可以进行下一步



6.使用`make -j14`开始编译，其中`-j`后面的数字为你要分配的线程数，如14线程。(根据你的虚拟机分配了多少核心决定，不能超过分配的核心数，也不建议分配满，如给了16核心则使用14线程即可)

这个过程是最容易出错的地方，如果出现错误导致不能编译通过，请查找下面的"错误排查"分段来查找解决方案。

如果编译成功(进度达到[100%])，则执行第七步



7.开始安装，使用`make install `

等待安装成功即可，基本不会出错



8.配置环境变量(建议配置)

​	没有配置环境变量依然可以使用，不过为了避免未知的错误还是建议配置

​	和在windows下配置JRE或者JDK一样，OpenCV也需要配置环境变量。

​	如果没有安装VIM，请执行`sudo apt install vim`

​	8.1	使用vim打开pkgconfig	`sudo vim /etc/profile.d/pkgconfig.sh `

​	8.2	按`i`键进入插入模式，在末尾插入

​			`export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH`

​	8.3	按`ESC`键退出输入模式，按`Shift`+`:`键后，输入`wq`回车即保存退出

​	8.4	更新环境变量

​			`sudo source /etc/profile`

​	输入`pkg-config --libs opencv4`查看是否成功

9.配置动态环境(建议配置)

​	理由同上。

​	9.1	输入`sudo vim /etc/ld.so.conf.d/opencv4.conf`

​	9.2	在末尾插入	`/usr/local/lib` ，vim操作同上

​	9.2	启用配置	`sudo ldconfig`

---

### 至此配置完毕

---

## 错误排查

* make阶段 出现错误:`boostdesc_bgm.i:没有那个文件或者目录`
  * 也可能出现确实其他`***.i`的问题，路径为`~~~~/opencv4.5.0/opencv_contrib4.5.0/modules/xfeatures2d/src`的问题，都可以使用此方法解决
  * 问题原因:contrib包缺失文件(官方源也缺失，不知道为何)
  * 解决方法:下载群内的`xfeatures2d.zip`,将解压出来的`***.i`文件放到`~~~~/opencv4.5.0/opencv_contrib4.5.0/modules/xfeatures2d/src`后，重新回到`第六步`即可



* make阶段 出现错误:`xfeatures2d/test/test_detectors_regression.impl.hpp:没有那个文件和目录`

  * 问题原因:编译时找不到该文件(在官方源下根本没有这个文件,很神秘)

  * 解决办法:回到OpenCV目录，复制`~~~/OpenCV4.5.0/modules/features2d`文件夹到你的`build`文件夹，重新回到`第六步`即可 

    

* make阶段卡死

  * 问题原因：make分配的线程数太高，或者计算机分配的虚拟机的核数太少
  * 解决办法:你自己懂

* 待发现.....



