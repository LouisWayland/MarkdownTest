# 基于Qt的GUI开发 #
- [移植tslib](#tslib)
- [编译](#con)
## <span id="tslib">移植tslib</span> ##
**tslib是电阻式触摸屏用于校准的一个软件库，是一个开源的程序，能够为触摸屏驱动获得的采样提供诸如滤波、去抖、校准等功能，通常作为触摸屏驱动的适配层，为上层的应用提供了一个统一的接口。**  
### 编译环境搭建 ###
`sudo apt install gcc-aarch64-linux-gnu`

`sduo apt install g++-aarch64-linux-gnu`

`sudo apt install gcc-arm-linux-gnueabihf`

`sduo apt install g++-arm-linux-gnueabihf`

`sduo apt install git make gcc g++`

`sudo apt install autoconf`

`sudo apt install automake`

`sudo apt install libtool`

### 下载源码 ###
[https://github.com/libts/tslib](https://github.com/libts/tslib)  
`git clone https://github.com/libts/tslib`

### 编译移植 ###
**生成configure**  
`./autogen.sh`

**创建配置脚本build.sh**   

    	#!/bin/bash
    	./configure \
    	--host=aarch64-linux-gnu \
    	--prefix /opt/tslib
    
    	make && make install

**<span id="con">编译</span>**  
`su root`  
`./build.sh`  

### Qt移植增加tslib支持 ###
configure配置 增加-tslib及-I/opt/tslib/include -L/opt/tslib/lib  

添加tslib路径  
`vim qbase/mkspecs/linux-aarch64-gnu-g++/qmake.conf` 
  
 
    	QMAKE_INCDIR += /opt/tslib/include  
    	QMAKE_LIBDIR += /opt/tslib/lib
    

