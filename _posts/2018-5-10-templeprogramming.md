---
layout: post
title:  "模板元编程"
date:   2018-05-10 
categories: 动动手
header-style: text
tags: temple
---
* content
{:toc}

### 动手实践模板

 C++ 模板可以做以下事情：编译期数值计算、类型计算、代码计算（如循环展开），其中数值计算实际不太有意义，
而类型计算和代码计算可以使得代码更加通用，更加易用，性能更好（也更难阅读，更难调试，有时也会有代码膨胀问题）




**源码：**
```c++

/**************************************************
* Author:Q&H
* Time:2018.5.10
* Source: 
* Describe: 
**************************************************/
#include <iostream>
template <typename T>			//1. 函数模板
T Compare(T a, T b)
{
	T res = a > b ? a : b;
	return res;
}

class SrcOpertClass				//2.类模板
{
public:
	int Add(int a, int b ){ return  a +b ;}
	int Multi(int a , int b){return  a * b;}
	double Add(double a, double b ){ return  a +b ;}
	double Multi(double a , double b){return  a * b;}
};
template<typename T>	
class TempOpertClass
{
public:
	T Add(T a, T b ){ return  a +b ;}		// 模板代替 -- 函数重载
	T Multi(T a , T b){return  a * b;}
};
/////////////////////////////////////////////// 3. 模板特例化
template<typename T, int N>				// 通例
class Vector
{
public:
	T _v[N];
};
template<>				// 特例化1
class Vector<double, 4>
{
public:
	double _v[4];
};
template<int N>			// 特例化2
class Vector<double, N>
{
public:
	double _v[N];
};
template<typename T>	// 特例化3
class Vector<T, 888>
{
public:
	T _v[888];
};
int T_templedemo()
{
	cout<< Compare<int>(10 , 8)<<endl;	// 函数模板
	SrcOpertClass soc;					// 类模板s
	TempOpertClass <int> toc;			
	cout<<soc.Add(10.1 , 25.6)<<endl;
	cout<<toc.Add(1,29)<<endl;

	const static int N = 8;
	Vector<int,N> vec;					// 通例
	vec._v[0] = 100;
	cout<<vec._v[0]<<endl;

	Vector<double,4> vec_1;				// 类型，数组大小都不变
	for(int i = 0 ; i < sizeof(vec_1._v)/sizeof(vec_1._v[0]) - 1; i ++)
	vec_1._v[i] = i + 10.11;
	cout<<vec_1._v[89]<<endl;			// 数组越界

	Vector<double, 10> vec_2;			// 模板特例化2	类型不变，数组大小改变
	for(int i = 0 ; i < sizeof(vec_2._v)/sizeof(vec_2._v[0]) - 1; i ++)
	vec_2._v[i] = i + 10.11;
	cout<<vec_2._v[5]<<endl;

	Vector<unsigned int, 888> vec_3;	// 模板特例化3  类型改变,数组大小固定
	for(int i = 0 ; i < sizeof(vec_3._v)/sizeof(vec_3._v[0]) - 1; i ++)
	vec_3._v[i] = i + 11;
	cout<<vec_3._v[5]<<endl;
	
	return 0;
}
```

Result:
![](https://raw.githubusercontent.com/7huang/Utility/master/img-source/temple_1.png)









