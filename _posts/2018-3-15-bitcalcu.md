---
layout: post
title:  "一个有趣的实验"
date:   2018-3-15 
categories: 动动手
tags: bitcalcu
---
* content
{:toc}

---

符号		| 描述 	| 运算规则
| ----- | -----	|			 
**&** 	| 与    | 两个标识位都为1时，结果才为1					
**丨** 	| 或    | 两个标识位都为0时，结果才为0					 
**^** 	| 异或	| 两个标识位相同为0，相异为1					 
**~** 	| 取反 	| 0变1，1变0								 
**<<** 	| 左移 	| 各二进制位全部左移若干位，高位丢弃，低位补0	 
**>>** 	| 右移	| 各二进制位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移）	


计算过程：

outbin(17);			|	// 00000000000000000000000000010001
outbin(4294967295);	|	// 11111111111111111111111111111111
outbin(4294967296);	|  	// 00000000000000000000000000000000
outbin(4294967297);	|	// 00000000000000000000000000000001
outbin(17);			|	//00010001
cout<<(17<<1)<<endl;|	//34
outbin(34);			|	//00100010








左移相当于乘2 ，当然右移就是相当于除以2了

**位存储状态:**

	枚举标识数据存储下标.这里若所在位为1，为活跃状态。
	通过attach_mask |= 1 << ACTION_TYPE_AGE_INFO 形式将数据标识活跃状态
	通过if (0 != (attach_mask & (1 << i)))做判断，是否数据为活跃状态
	通过attach_mask &= ~(1 << ACTION_TYPE_AGE_INFO) 对状态修改为0


```c++

	#include<iostream>
	#include <bitset>
	using namespace std;
	enum StutasInfoStorage
	{
		ACTION_TYPE_LEVEL_INFO = 1,
		ACTION_TYPE_AGE_INFO = 2,
		ACTION_TYPE_MAX_NUM,
	};
	const static int ATTACH_DATA_LENGTH = 2048;
	int				attach_mask;
	char			attach_data[ATTACH_DATA_LENGTH];
	void outbin(int x) { cout<<bitset<sizeof(int)*8>(x)<<endl; }	// 二进制输出 
	void outoct(int x) { cout<<oct<<x<<endl; }						// 八进制输出  
	void outhex(int x) { cout<<hex<<x<<endl; }						// 十六进制输出 
	void chartobit()
	{
		unsigned char code = 255; 
		for(int i=7; i>=0; i--) 
		{  
			std::cout << ((code >> i) & 1);      
		}  
		std::cout << std::endl; 
	}
	void GetLevelInfo()
	{
		attach_mask |= 1 << ACTION_TYPE_LEVEL_INFO;  
	}
	void GetAgeInfo()
	{
		attach_mask |= 1 << ACTION_TYPE_AGE_INFO;
	}
	void OutActionTypeInf()
	{
		for (int i = 0; i < ACTION_TYPE_MAX_NUM; i ++)
		{
			if (0 != (attach_mask & (1 << i)))
			{
				cout<<"第"<<i<<"种信息可以获取"<<endl;
			}
		}
	}
	void T_bitcalcu()
	{	
	
		/**************************************************
		//& | ` ~	
		result:
		00000000000000000000000000000000
		00000000000000000000000000110011
		11111111111111111111111111101110
		00000000000000000000000000110011
		**************************************************/
		cout<<"//& | ` ~"<<endl;
		outbin((17&34));		
		outbin(17|34);
		outbin(~17);
		outbin((17^34));
		attach_mask = 0;
		GetLevelInfo();
		outbin(attach_mask);
		GetAgeInfo();
		outbin(attach_mask);
		OutActionTypeInf();
		attach_mask &= ~(1 << ACTION_TYPE_AGE_INFO);
		OutActionTypeInf();
	}
```