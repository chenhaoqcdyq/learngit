# weekly report[10.7-10.14]

# 学习内容

## 一、《视觉SLAM十四讲》学习与环境配置

### 第二讲 初识SLAM

<img src="/img/SLAM.png" />

### Ubuntu18.04安装

* #### 虚拟机安装

  虚拟机软件版本：VMware® Workstation 16 Pro

  版本号：Ubuntu18.04

  > 安装参考：http://t.csdn.cn/P71XR
  >
  > 虚拟机分辨率自适应问题参考：http://t.csdn.cn/LDvo5

* #### C++程序编译器环境搭建

  在主文件夹建立一个新的文件夹，命名为**slambook**，在此文件夹中进入终端，新建cpp文件：

  ```c
  touch main.cpp
  ```

  打开文件，输写一个简单的程序验证是否安装了编译器：

  ```c++
  #include <iostream>
  using namespace std;
  
  int main(int argc,char **argv){
  	cout<<"Hello SLAM"<<endl;
  	return 0;
  }
  ```

  关闭编辑器，安装g++编译器，并用g++编译器把C++文件编译成可执行文件，在终端输入：

  ```c
  sudo apt-get install g++
  g++ main.cpp
  ```

  出现了一个`a.out`的文件，在终端输入：

  ```c
  ./a.out
  Hello SLAM!
  ```

  即可正常运行C++文件。

  * ##### 实现虚拟机与主机复制粘贴功能

    > 参考博客：http://t.csdn.cn/40gBi

* #### 使用cmake

  新建一个`CMakeLists.txt`文件：

  ```c
  touch CMakeLists.txt
  ```

  在CMakeLists.txt文件中写入：

  ```
  # 声明要求的cmake的最低版本
  cmake_minimum_required( VERSION 2.8 )
  
  # 声明一个cmake工程
  project( helloslam )
  
  # 添加一个可执行程序
  # 语法：add_executable( 程序名 源代码文件 )
  add_executable( main main.cpp )
  ```

  在当前目录下进入终端，调用cmake进行编译：

  ```c
  cmake .
  ```

  运行结果如下所示：

  ![image-20221009181338394](/img/01.png)

  然后用`make`指令对工程进行编译：

  ```c
  make
  ```

  结果如图所示：

  ![image-20221009181610887](/img/02.png)

  此时生成了一个`main`文件（在`CMakeList.txt`文件中声明的那个可执行程序的名字），执行他：

  ```c
  % ./main
  Hello SLAM!
  ```

  到此就能够运行文件了。

  为了让中间文件放在一个中间目录中，更常用的编译cmake工程的做法如下：

  ```c
  mkdir build
  cd build
  cmake ..
  make
  ```

* #### 使用库

  在/lambook/ch1中新建cpp文件：

  ```c++
  // 这是一个库文件
  #include <iostream>
  using namespace std;
  
  void printHello(){
    cout << "Hello SLAM"<< endl;
  }
  ```

  ![image-20221010173121599](/img/03.png)

  

  这个库中提供了一个`printHello`函数，调用此函数可以输出一条信息。在CMakeLists.txt里加上如下内容：

  ```c
  add_library( hello libHelloSLAM.cpp )
  ```

  ![image-20221010212942052](/img/04.png)

  这条命令告诉cmake，我们想把这个文件编译成一个叫做`hello`的库。然后，和上面一样，使用cmake编译整个工程：

  终端输入：

  ```c
  cd build
  cmake ..
  make
  ```

  ![image-20221010212923534](/img/05.png)

  这时在build文件夹中就会生成一个`libhello.a`文件，这就是我们得到的库。

  ![image-20221010213011428](/img/06.png)

  在Linux中，库文件分为**静态库**和**共享库**两种。静态库以.a为后缀名，共享库以.so结尾。所有库都是一些函数打包后的集合，差别在于静态库每次被调用都会生成一个副本，而共享库则只有一个副本，更省空间。

  * ##### 如果要生成**共享库**，只需将语句改为：

  ```c
  add_library( hello_shared SHARED libHelloSLAM.cpp )
  ```

  ![image-20221010213522329](/img/07.png)

  为了让别人知道库中的内容，我们需要提供一个**头文件**，说明这些库中的内容。

  ```c
  #ifndef LIBHELLOSLAM_H_
  #define LIBHELLOSLAM_H_
  
  void printHello();
  
  #endif
  ```

  这样，根据这个文件和我们刚才编译得到的库文件，就可以使用`printHello`函数了。最后写一个可执行程序`useHello.cpp`来调用这个简单的函数：

  ```c++
  #include "libHelloSLAM.h"
  // 打印hello的函数
  int main(){
  	printHello();
  	return 0;
  }
  ```

  然后在`CMakeLists.txt`中添加一个可执行程序的生成命令，链接到刚才使用的库上：

  ```c
  add_executable(useHello useHello.cpp)
  target_link_libraries(useHello hello_shared)
  ```

  ![image-20221010221636249](/img/08.png)

  终端cmake：

  ```c
  cd build
  cmake ..
  make
  ```

  后得到的结果：

  ![image-20221010221212902](/img/09.png)

  > 在配置过程中碰到了这个问题：
  >
  > ![image-20221010220939137](/img/10.png)
  >
  > 后来发现是`CMakeLists.txt`拼写出了问题...

  最后调用：

  ```c
  % ./useHello
  ```

  结果：

  ![image-20221010221600304](/img/11.png)
  
   

### 配置CLion

**下载与安装教程参考：**

**参考博客：**[CLion安装配置与学生认证](http://t.csdn.cn/Hf5qD)

> 下载CLion后，申请教育版后，在配置上出现问题：Authorization failed（验证不通过），查了大量资料后发现是host的问题
>
> 参考博客：[jetbrains的goland、IDEA、pycharm等软件授权失败（Authorization failed）的解决方案](http://t.csdn.cn/U4AXj)
>
> 最终结果：
>
> ![image-20221012175633593](/img/12.png)

### 配置KDevelop

1. 下载安装编译器

```c
sudo apt-get build-dep gcc
sudo apt-get install build-essential
```

2. 安装kdevelop

```c
sudo apt-get install kdevelop
```

3. 安装cmake等

```c
sudo apt-get install automake autoconf g++ libtool cmake
```

## 二、Git学习

### Git下载参考

> 参考博客：[Git 详细安装教程（详解 Git 安装过程的每一个步骤）](http://t.csdn.cn/1zC1a)

### Git学习

> 参考学习网址：[Atlassian 的简明 Git 教程](https://www.atlassian.com/git/tutorials)

基础用法：

创建一个版本库：

```c
$ mkdir learngit
$ cd learngit
$ pwd
```

将目录变成git可以管理的仓库

```c
$ git init
```

将文件添加到仓库只需要两步：

第一步，用命令`git add`将文件添加到仓库

```C
$ git add README.md
```

第二步，用命令`git commit`将文件提交到仓库：

```
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.md
```

关于`git commit`命令，`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

> 在与github同步时遇到问题，出现输入password的选项，输入github密码后无果，查阅资料发现是ssh没有配置好，参考博客：
>
> [github 配置了公钥依旧提示git@github.com‘s password: Permission denied, please try again. 的解决办法](http://t.csdn.cn/WkIJs)
