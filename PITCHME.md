# 标题
---
## dubbo的基础介绍，与现在服务框架区别
---
## dubbo使用
---
- #### 服务提供者  
	*启动类与现在对比，没有改动
会默认使用lz-dubbo-config配置，加载默认配置，如果有必要可以启动参数-Ddubbo.config.key来修改配置,接口实现类如下，可以通过在ServiceProvider中指定version和dubbo的值，否则使用1.0.0和default作为默认值*
---?gist=gymonkey/f01ba06739b232bad30e8974c2fee49b#file-consumer-example-java-L2&lang=java&color=white&title=测试标题  
- #### 服务调用者
	*服务调用者与现在相比，代理类构造器初始化方法没有改动，与服务提供者类似，会使用默认的lz-dubbo-config配置，如果不明确指定，会使用1.0.0和default作为version和group默认值* 
 

---
- #### 配置

	| 配置项 | 作用 |
	|----------|----------|
	|dubbo.reference.retries|调用重试次数|
	|dubbo.reference.connections|与服务提供者的连接数|
	|dubbo.reference.loadbalance|负载均衡策略|
	|dubbo.protocol.threads|服务提供者线程数|
	|dubbo.protocol.iothreads|io线程数|
---

- #### 扩展点
	|扩展点|用途|
	|---|----|
	|com.alibaba.dubbo.rpc.cluster.RouterFactory|从多个服务提者方中选择一个进行调用|
	|com.alibaba.dubbo.rpc.cluster.LoadBalance|从多个服务提者方中选择一个进行调用|
---
## 我们对dubbo的改造
- #### dubbo代码的分层
	![dubbo代码的分层](http://wx2.sinaimg.cn/mw690/0060lm7Tly1fwdkup8d7yj30p00iqjx2.jpg)  
---
- #### 兼容现有逻辑（基于domain-op调用和现有的filter机制，使用guice作为ioc容器）  
	*config 配置层：封装获取默认配置，自定义配置的覆盖(LzDubboConfigLoader)*  
	*proxy 服务代理层：兼容基于domain-op的调用方式（LzDubboProxyFactory）*  
	*protocol 远程调用层：兼容现有的filter机制（LzDubboProtocol）*  
	*transport 网络传输层：兼容目前domain-op的调用方式，传输domain、op、request-response的protobuf对象（LzDubboTransporter）*  
	*container 容器层：兼容guice容器（LzDubboContainer）*
---  
- #### 灰度发布（在有dubbo版本提供时优先使用dubbo，否则使用现有方式）  
	*registry 注册中心层：记录已提供的dubbo服务（LzDubboRegisty）*
---
## 后续的开发工作
---
# 结尾
