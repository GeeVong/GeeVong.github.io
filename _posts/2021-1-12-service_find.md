---
layout: post
title:  "服务发现"
date:   2021-1-12 
header-style: text
categories: game
tags: 总结
---

## 服务发现

#### 疑问 

资料：https://zhuanlan.zhihu.com/p/34332329

对于服务发现，第一映象是etcd，consul，nats这些工具，假如自己实现一个服务发现，应该怎么做呢。首先，浮现脑海中的问题就是，什么是服务发现？它的需求来源是什么？

通过上面对服务的创建过程的分析：

​	1.服务会在service_pool中做存储（K: serviceID--string类型   v：func(serviceInfo *ServiceInfo) ServiceInterface 函数变量，返回值为ServiceInterface），并且返回一个ServiceInterface实例

​	2.通过拿到的ServiceInterface实例在管理器中对服务进行注册



资料：

​		1.服务发现存在以下角色：服务提供者、服务消费者和服务中介，服务中介就是一个字典，字典里有很多key/value键值对，key是服务名称，value是服务提供者的地址列表。服务注册就是调用字典的Put方法塞东西，服务查找就是调用字典的Get方法拿东西。当服务提供者节点挂掉时，要求服务能够及时取消注册，比便及时通知消费者重新获取服务地址。



所以现在我们知道一下信息：

​	1. 服务提供者就是服务本身， 服务中介就是服务池，服务消费者也是服务

​	2.service_mgr和service_pool中会维护多个服务的状态，服务之间（假设一个进程部署一个服务）可能存在：1对1调用，1对n调用，n对n调用。



### 服务发现启动	

​	我们在ServeiceMgr 初始化过程中，会对进程监控器实例化，NewRedisServiceWatcher，当前服务在启动的时候，加入到TableList

```go
    go func() {
      lgames.LogInfo(self.GetProcessID(), "分布式模式")
      defer lgames.PanicDebugStack("")
      PathAppServer = fmt.Sprintf("%s/%s/%s/%s", self.ServerInfo.GameID, env, version, ETCD_PATH_PROCESS)
      //etcdHosts := appConfig.INIConfig.GetString("etcd", "host", "")
      //var etcdHostList = lgames.StringToList(etcdHosts, "list<string>", ";").([]string)
      //self.serviceWatcher = NewServiceWatcherETCD(self.ServerInfo.ProcessID, self.ServerInfo.GameID, serverDesc, PathAppServer, etcdHostList)
/*    self.serviceWatcher = NewMySqlServiceWatcher(self.ServerInfo.ProcessID, self.ServerInfo.GameID, serverDesc, PathAppServer, dbConfig, false)
      self.serviceWatcher.StartRun()
      self.serviceWatcher.NewTable(PathAppServer)*/
      id := fmt.Sprintf("%s/%s", PathAppServer,self.ServerInfo.ProcessID)
      value, _ := jsoniter.Marshal(self.ServerInfo)
/*    globalServiceWatcher := NewMySqlServiceWatcher(id,
         self.ServerInfo.ProcessID,  string(value), PathAppServer, globalDBConfig)
      globalServiceWatcher.StartRun()*/

      self.globalServiceWatcher = NewRedisServiceWatcher(id,self.ServerInfo.ProcessID,  string(value),PathAppServer,constant.RDS_MAIN)
      self.globalServiceWatcher.StartRun()
      //self.globalServiceWatcher.NewTable(PathAppServer)
      //self.createWatcher(PathAppServer, self.GetProcessID(), string(value))
   }()
```



### 服务状态维护

redis 服务发现状态数据结构

```go
type ServiceWatcher_Redis struct {
   ServiceWatcher
   //-----------------------------
   lastInsertId uint32
   redisCache cache.Cache
   nextKeepAliveTime   int64
   nextClearExpireTime int64
   nextWatchServerTime int64
   expireApps []string
   TableKeyMap    map[string]string  // 服务列表

}

type ServiceWatcher struct {
	common.ThreadObject
	//--------------------------
	WatcherID   string  // 进程id  fmt.Sprintf("%s/%s", PathAppServer,self.ServerInfo.ProcessID)
	WatcherName string	// ip+端口 fmt.Sprintf("%s:%d", serverIpHost, serverIpPort)
	ServiceDesc string  // 当前进程信息 value, _ := jsoniter.Marshal(self.ServerInfo)
	DefaultPath string  // 标识当前节点类型 fmt.Sprintf("%s/%s/%s/%s", self.ServerInfo.GameID, env, version, ETCD_PATH_PROCESS)
    					// bingqiu_yn_dl+kaifa+20190510+appServer 用于区分是否在同一个集群
	TableList []string  // append(newService.TableList,table)  bingqiu_yn_dl+kaifa+20190510+appServer
	isStartWatch bool
}


func NewRedisServiceWatcher(id string, name string, info string, table string, cacheName string) IServiceWatcher {
	newService := &ServiceWatcher_Redis{}
	newService.WatcherID = id
	newService.WatcherName = name
	newService.DefaultPath = table
	newService.TableList = make([]string,0)
	newService.TableList = append(newService.TableList,table)
	newService.ServiceDesc = info
	newService.TableKeyMap = make(map[string]string)
	newService.redisCache  = db.Mgr().FindCache(cacheName)
	return newService
}
```

TickRun 检查服务列表，4秒做一次检查， loadAppServer更新TableList

keepAlive 本地服务状态检查





### 服务间如何建立连接，如何判断两个服务不是是在同一个集群

当本地服务完成ServiceWatcher 实例的创建之后，通过TickRun 定时拉取redis所有的APP信息:

​	1.对self.TableKeyMap检查，如果有服务挂掉，关闭连接

​	2.对tableKeyMap检查，如果发现新的服务，建立连接





#### 发起连接

​	

### 总结

分布式系统中，redis做服务发现，所有节点依赖redis集群做服务的发现。当有一个新的服务启动的时候，需要到redis RDS_MAIN集群拉取缓存信息

