---
layout: post
title:  "充值系统设计"
date:   2021-2-11 
header-style: text
categories: game
tags: 总结
---

# 充值系统设计



## 写在前

下面是王者荣耀的充值系统，这是对于玩家可见部分，玩家点击购买，iso弹窗显示指纹支付，指纹验证成功，完成订单支付，客户端收到点券，去下支付，提示支付失败。那么当中到底是怎么完成这一整套流程的呢？下面就来探究

![img](https://raw.githubusercontent.com/seven1H/pic/master/3ACB7F4E-40AD-4B24-BA76-34F9A4DC9A6E.png)



## 流程概述

- 充值系统存在交互方：
  - 客户端 Client
  - 游戏服务器 GameServer
  - 平台 Third-Sdk （如果是多方发行，平台负责集成多渠道）

- 流程图

![image-20200925153637345](https://raw.githubusercontent.com/seven1H/pic/master/image-20200925152926833.png)

- 流程概述：

​	客户端购买商品->游戏服务器校验->游戏生成订单->下发给客户端->客户端拉起订单->支付->SDK平台通知游戏服务器下单成功->游戏服务器发货，订单完成

- 具体操作流程：
  - 订单请求
    - 客户端**Client-001** 登录到游戏，进入充值系统，发起购买请求 (玩家点击了充值648按钮)  
  - 订单
    - 上面操作完成之后，会在服务端生成一个**充值订单**（服务器订单）
  - 玩家支付
    - 客户端接收服务器返回的订单信息或者去服务器拉取订单信息（订单支付时间cd）， 转到平台，平台识别属于哪个渠道，调用对应渠道sdk，进行支付, 用户完成订单支付，**平台订单**生成。
  - 服务器校验，发货。
    - 平台通过客户端带过来的订单信息，回调到服务器( 如何实现回调?)，进行校验，无误之后服务器发货给玩家

### 对于研发端，我们需要关注的点：

 - 客户端交互
   	- 下单api：购买处理，gen_order,update 角色充值信息，返回订单信息给client
   	- 发货api：推送
 - 平台回调处理：
   	- http？？？
 - 数据状态处理
   	- 玩家充值订单管理
   	- 玩家充值数据管理

## 异常处理

系统运行过程中可能存在的问题

- 支付订单失败如何处理？
- 购买限购订单，下单支付未发货，多次支付问题（充值，支付之后没有立即到账，可能要半天才到账）解决方式，对限购物品进行加锁，订单未完成，不允许再次购买

- 如果充值过程中，关服了
  - 服务器关服了，就是充值后台无法推送订单过来了，所以充值后台再推送失败后，会不断重试
- 如果充值过程中，角色不在线了
  - 角色不在线，是推送成功了，但是角色数据不在线，所以先处理充值订单状态，然后再上线的时候再发货

## 充值系统数据库表设计

- 充值订单数据结构： 对订单的一些信息进行记录

```go
const (
	RoleRechargeOrder_OrderType_Cash    = "cash"    // 现金充值
	RoleRechargeOrder_OrderType_Virtual = "virtual" // 虚拟充值
)

// 角色充值订单
type RoleRechargeOrder struct {
	OrderId    string   // 订单ID
	OrderType  string   // 订单类型. cash
	RoleId     int64    // 角色ID
	TickerId   string   // 流水号
	ChOrderId  string   // 渠道订单ID
	GoodsType  int32    // 购买礼包ID
	GoodsId    int32    // 购买礼包ID
	Price      int32    // 订单价格
	Amount     float64  // 支付金额
	GameKey    string   // 游戏标识
	Channel    string   // 渠道标识
	Uid        string   // 渠道账号唯一标识
	FinishTime int64    // 订单发货时间
	PayTime    int64    // 订单支付时间
	CreateTime int64    // 订单创建时间
}
```



- 角色充值信息

```go

// 角色充值信息
type RoleRechargeInfo struct {
	RoleId        int64
	FirstPay      int64                          // 首充时间
	FirstPayAward int64                          // 首充奖励领取时间
	TotalAmount   float64                        // 充值总额
	TotalAwards   []int32                        // 已领取的累充奖励
	BuyQualify    map[int32]int64                // 礼包购买资格, map[礼包ID]生效开始时间
	BuyGift       map[int32]*RechargeGift        // 礼包购买信息
	GrowGifts     map[int32]*RechargeGrowGift    // 成长礼包状态
	MonthCards    map[int32]*RechargeMonthCard   // 月卡状态
	VipTransform  bool                           // 是否转换VIP经验
	VipAwards     map[int32]int64                // VIP奖励礼包
	PassCard      map[int32]*RechargePassCard    // 通行证
}

// 充值礼包购买记录
type RechargeGift struct {
	Id    int32   // 礼包Id
	Type  int32   // 礼包类型 (可以定义礼包类型)
	Total int32    // 总累计购买次数
	Num   int32   // 本期购买次数
	Time  int64   // 本期时间戳
}

// 成长礼包状态
type RechargeGrowGift struct {
	Id      int32             // 成长礼包ID
	BuyTime int64             // 购买时间
	Stage   int32             // 当前阶段
	Awards  map[int32]int64   // 领取记录 map<任务ID, 领取时间>
}

// 月卡状态
type RechargeMonthCard struct {
	Id         int32            // 月卡ID
	BuyTime    int64            // 购买时间
	ExpireTime int64            // 到期时间
	Awards     map[int64]int64  // 奖励领取
	VipAwards  map[int64]int64  // VIP奖励
}

// 充值令牌信息
type RechargePassCard struct {
	BuyTime   int64             // 购买时间
	StartTime int64             // 开始时间
	Exp       int32             // 当前经验
	Level     int32             // 当前等级
	Awards    map[int32]int64   // 奖励领取
	AdvAwards map[int32]int64   // 高级奖励
}
```





