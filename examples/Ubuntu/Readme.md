# 	Ubuntu例程

​	本文档介绍了如何在Ubuntu环境中读取Hi229/226 的数据，本路径提供了c语言例程代码，生成的可执行文件用于读取模块的数据。

​	测试环境： Ubuntu 16.04

​	测试设备：超核调试版 	HI226	HI229

## 查找USB-UART设备

​	因为Ubuntu 系统自带CP210x的驱动，所以不用专门去安装相应串口驱动。将调试版连接到电脑上时，会自动识别设备。识别成功后，会在dev目录下出现一个对应的设备文件。

​	检查系统是否识别到USB-UART设备：

​	1、打开Ubuntu系统，按下 __ctrl + alt + t__ 打开命令行窗口

​	2、在窗口上输入 `cd /dev`  切换到dev目录下，这个目录下，是一些设备文件。

​	3、然后在dev目录下执行`ls` 这个命令是查看当前目录下都有哪些文件，然后按下 Enter 键，就会出现设备文件名称，在这些文件名称中，主要关心 **ttyUSB** 这个设备文件。后面数字代表USB设备号，由于Ubuntu USB设备号为从零开始依次累加，所以多个设备每次开机后设备号是不固定的，需要确定设备的设备号。下面用两张图片来描述:

![](https://github.com/hipnuc/products/blob/master/examples/Ubuntu/img/1.png)

​	上图为没有插入USB设备的情况，这个时候，dev目录下并没有名为 __ttyUSB__ 文件，插入USB线，连接调试板，然后再次执行`ls`：

dev目录下多了几个文件名称, 如图：

![](https://github.com/hipnuc/products/blob/master/examples/Ubuntu/img/2.jpg)

​	**ttyUSB0** 文件就是调试版在ubuntu系统中生成的设备文件，对它进行读写，就可以完成串口通信。这个文件名称我们把它记下来。后面的数字是不固定的，有可能为 **ttyUSB1**  或 **ttyUSB2** 等。

## 波特率设置

​	在Ubuntu环境中，波特率支持到115200,460800,921600，本例程使用的是115200。

​		如果需要输出帧率超过100Hz，则需要需要修改main.c文件中的options.c_cflag参数，改为更高的波特率。

![](https://github.com/hipnuc/products/blob/master/examples/Ubuntu/img/5.png)

​		如图，在第83行，将B115200修改为B460800或者是B921600。

## 编译并执行

​	我们开始在Ubuntu环境下生成一个可执行文件，专门用来解析模块的数据：

​	首先在Ubuntu系统中，按下 __ctrl + alt + t__ 快捷键，在弹出的窗口上，执行`mkdir hipnuc` 建立hipunc目录,然后将本文档所在目录下的所有文件复制到 __hipnuc__ 目录下。

​	执行`make`, 生成可自行文件`main`。 并执行`sudo ./main ttyUSB0`:

​	执行成功后，会出现这个画面：

![](https://github.com/hipnuc/products/blob/master/examples/Ubuntu/img/3.png)

​	这个画面上的数字会随着模块位置的改变而发生变化。

​	如果后期修改了这些文件，需要执行`make clean`命令，进行清理之前生成的旧 __.o__ 和 __main__ 文件，然后再执行`make`，重新生成 __main__ 这个可执行文件。

​	如果后期您需要在本路径上添加其他文件，配合使用，请打开 __Makefile__ 文件，在第一行的后面加上后添加文件的链接文件名，例如添加append_file.c文件，那么在 __Makefile__ 中第一行后面追加append_file.o文件名。如果后加的文件还需要链接第三方的库，请在第二行的后面添加库名字。格式为 __-l+lib_name__  ("l" 是“L"的小写的英文字母)。

​	如果出现：

![](https://github.com/hipnuc/products/blob/master/examples/Ubuntu/img/4.png)

​	表示未能找到串口，需要回到**《查找USB-UART设备》一节** 确认USB-UART设备已经被ubuntu识别。

