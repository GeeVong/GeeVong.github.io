---
layout: post
title:  "编译工具使用"
date:   2019-9-4 
header-style: text
categories: 动动手
tags: tool
---

## 使用gcc/g++编译代码

```cpp
#include<iostream>
using namespace std;
int main(){
  cout<<"hello world"<<endl;
  return 0;
}
```

```make
hqdeMac-mini:example1 hq$ g++ hello.cpp -o hello
```

```make
hqdeMac-mini:example1 hq$ ./hello 
hello world
```



## makefile 使用

#### 创建并编辑makefile文件(末尾需要加空格)

```make
m_hello: hello.cpp
	g++ hello.cpp -o hello_m


```

#### 执行make命令，运行可执行文件 hello_m

```make
hqdeMac-mini:example1 hq$ make
g++ hello.cpp -o hello_m
hqdeMac-mini:example1 hq$ ls
hello.cpp	hello_m		makefile
hqdeMac-mini:example1 hq$ ./hello_m 
hello world
```



#### makefile 还有很多丰富的使用方式 todo

这里有个简易笔记连接[Link](http://note.youdao.com/noteshare?id=86717e7b9ca72a124c8de079bb1ddae8)

## Cmake使用

#### 编写CMakeList.txt 文件（todo 熟悉cmake语法）

```cmake
cmake_minimum_required(VERSION 3.14)
project(TestCmake)
set(cmake_build_type debug)
set(CMAKE_CXX_STANDARD 14)
add_executable(
        TestCmake
        src/main.cpp
        src/a.cpp
        include/a.h
)
```



#### 通过(cmake .)命令生成 makefile文件。

```cmke
hqdeMac-mini:TestCmake hq$ cmake .
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/hq/CLionProjects/TestCmake
hqdeMac-mini:TestCmake hq$ ls
CMakeCache.txt          CMakeFiles              CMakeLists.txt          Makefile                bin                     cmake_install.cmake     include                 src
```



#### 通过(**make)**命令编译代码，生成TestCmake 可执行文件文件。

```cmke
hqdeMac-mini:TestCmake hq$ make
[ 33%] Linking CXX executable TestCmake
[100%] Built target TestCmake
hqdeMac-mini:TestCmake hq$ ls
CMakeCache.txt          CMakeLists.txt          TestCmake               cmake_install.cmake     src
CMakeFiles              Makefile                bin                     include
```

#### 运行可执行文件  ./TestCmake

```cmake
hqdeMac-mini:TestCmake hq$ ./TestCmake 
a is 5.000000, b is 25.000000
hqdeMac-mini:TestCmake hq$ 
```



## 测试代码

```cpp
// file a.h
// Created by hq on 2019/9/4.
//

#ifndef UNTITLED_A_H
#define UNTITLED_A_H
#include<math.h>

double get_sqrt(double var1);
#endif //UNTITLED_A_H

```



```cpp
// file a.cpp
// Created by hq on 2019/9/4.
//

#include"../include/a.h"
double get_sqrt(double var1)
{
    return sqrt(var1);
}

```



```cpp
#ifndef UNTITLED_MAIN_H
#define UNTITLED_MAIN_H
#include<stdio.h>
#include"../include/a.h"
int main()
{
    double b=25.0;
    double a=0.0;
    a=get_sqrt(b);

    printf("a is %lf, b is %lf\n",a,b);
    return 0;
}
#endif //UNTITLED_MAIN_H

```











