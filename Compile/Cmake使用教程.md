# Cmake使用教程

## 前置内容

- 官网下载cmake,配置环境变量
- vscode中安装cmake tool扩展,配置MinGW编译工具

## 在项目中使用Cmake构建编译和调试

- 新建`CMakeLists.txt`文件用于添加构建指令,具体语法细节查看官方文档和相关教程
- 打开命令面板输入`CMake:Select a Kit`选择构建方式,这里选择gcc相关版本使用MinGW构建,也可以使用VS构建,一般来说如果系统上面安装了VS,第一次build的时候会使用MSVS,所以需要先运行这个命令选择,默认的编译工具
- 命令面板输入`CMake:Build`构建任务,然后就会按照`CMakeLists.txt`的要求构建任务
- 调试和运行,在左下角有相应的按键,先在命令面板输入`CMake:Set Lauch/Debug Target`选择需要运行和调试的任务,之后就可以使用左下角的运行和调试按键运行和调试文件

![image-20250515144319506](https://gitee.com/hulu135289/Typora/raw/master/img/20250515144326872.png)

## 简单书写CMakeLists.txt

在根目录下输入以下内容,`add_subdirectory`加上需要编译的文件目录相对路径或者绝对路径

```cmake
# filepath: c:\Users\Dell\Desktop\C\submit\CMakeLists.txt
# CMakeLists.txt
cmake_minimum_required(VERSION 3.10)

# Set the project name
project(Code)

add_subdirectory(May/5-15/)
add_subdirectory(May/5-1/minesweep)
```

在相应的目录下加上需要生成可执行文件的文件,`add_executable`后面可执行文件名+相应的文件,支持多文件编译,多文件需要加多个文件名

```cmake
add_executable(bubbleSort bubbleSort.c)
add_executable(selectionSort selectionSort.c)
add_executable(test test.c)
```

```cmake
# filepath: c:\Users\Dell\Desktop\C\submit\May\minesweep\CMakeLists.txt
add_executable(minesweep game.c minesweeper.c)
```





