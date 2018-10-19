# Dubbo在荔枝的应用
---
## 一、dubbo的基础介绍，与现在服务框架区别
---
### 当前服务框架的架构
---
### dubbo的架构
![](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fwdp9x22o7j30ci08cdg2.jpg)
+++
0.服务容器负责启动，加载，运行服务提供者。  
1.服务提供者在启动时，向注册中心注册自己提供的服务。  
2.服务消费者在启动时，向注册中心订阅自己所需的服务。  
3.注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。  
4.服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。  
5.服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。
---
###  与现有服务框架的异同
+++
不再强依赖一个中心节点  
路由策略、负载均衡策略都集中在客户端  
更加完善的服务治理工具  
更加强悍的插件化功能  
---
## 二、dubbo的使用
---
### 依赖
```
<dependency>
  <groupId>fm.lizhi.commons</groupId>
  <artifactId>lz-commons-serviceclient</artifactId>
  <version>1.0.0-SNAPSHOT</version>
</dependency>
```
---
### 服务提供者  
*启动类与现在对比，没有改动
会默认使用lz-dubbo-config配置，加载默认配置，如果有必要可以启动参数-Ddubbo.config.key来修改配置,可以通过在ServiceProvider注解指定version和dubbo的值，否则使用1.0.0和default作为默认值*
---?gist=gymonkey/39c5a4bcf637bfecf74a9e597cb2a490&lang=java&color=white&title=%E6%8F%90%E4%BE%9B%E8%80%85%E4%BE%8B%E5%AD%90
---  
### 服务调用者
*服务调用者与现在相比，代理类构造器初始化方法没有改动，与服务提供者类似，会使用默认的lz-dubbo-config配置，如果不明确指定，会使用1.0.0和default作为version和group默认值* 
---?gist=gymonkey/f01ba06739b232bad30e8974c2fee49b&lang=java&color=white&title=%E8%B0%83%E7%94%A8%E8%80%85%E4%BE%8B%E5%AD%90
---
 ### 配置
+++
| 配置项 | 作用 |
|----------|----------|
|dubbo.reference.retries|调用重试次数|
|dubbo.reference.connections|与服务提供者的连接数|
|dubbo.reference.loadbalance|负载均衡策略|
|dubbo.protocol.threads|服务提供者线程数|
|dubbo.protocol.iothreads|io线程数|
---
### 扩展点
+++
|扩展点|用途|
|---|----|
|RouterFactory|从多个服务提者方中选择一个进行调用|
|LoadBalance|从多个服务提者方中选择一个进行调用|
---
## 三、我们对dubbo的改造
---
### dubbo代码的分层
![dubbo代码的分层](http://wx2.sinaimg.cn/mw690/0060lm7Tly1fwdkup8d7yj30p00iqjx2.jpg)  
---
### 兼容现有逻辑（基于domain-op调用和现有的filter机制，使用guice作为ioc容器）  
+++
*config 配置层：封装获取默认配置，自定义配置的覆盖(LzDubboConfigLoader)*  
*proxy 服务代理层：兼容基于domain-op的调用方式（LzDubboProxyFactory）*  
*protocol 远程调用层：兼容现有的filter机制（LzDubboProtocol）*  
*transport 网络传输层：兼容目前domain-op的调用方式，传输domain、op、request-response的protobuf对象（LzDubboTransporter）*  
*container 容器层：兼容guice容器（LzDubboContainer）*
---  
### 灰度发布（在有dubbo版本提供时优先使用dubbo，否则使用现有方式）
+++
*registry 注册中心层：记录已提供的dubbo服务（LzDubboRegisty）*
---
## 四、后续的开发工作
---
# Q&A
