---
layout:       post
title:        "Spring Boot健康检查异常"
subtitle:     "Solr health check failed"
date:         2019-08-07 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-27.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - SpringBoot
---

## Solr health check failed

最近接手一个项目，启动后报错如下：

{% highlight java %}
#s_logger#2019-08-07 18:07:05.354 [RMI TCP Connection(3)-10.152.2.52] WARN  o.s.boot.actuate.solr.SolrHealthIndicator-89 Solr health check failed #e_logger#
java.lang.NullPointerException: null
	at org.apache.solr.client.solrj.response.SolrResponseBase.getResponseHeader(SolrResponseBase.java:66)
	at org.apache.solr.client.solrj.response.SolrResponseBase.getStatus(SolrResponseBase.java:71)
	at org.springframework.boot.actuate.solr.SolrHealthIndicator.doHealthCheck(SolrHealthIndicator.java:50)
	at org.springframework.boot.actuate.health.AbstractHealthIndicator.health(AbstractHealthIndicator.java:84)
	at org.springframework.boot.actuate.health.CompositeHealthIndicator.health(CompositeHealthIndicator.java:98)
	at org.springframework.boot.actuate.health.HealthEndpoint.health(HealthEndpoint.java:50)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
	at org.springframework.util.ReflectionUtils.invokeMethod(ReflectionUtils.java:246)
	at org.springframework.boot.actuate.endpoint.invoke.reflect.ReflectiveOperationInvoker.invoke(ReflectiveOperationInvoker.java:76)
	at org.springframework.boot.actuate.endpoint.annotation.AbstractDiscoveredOperation.invoke(AbstractDiscoveredOperation.java:61)
	at org.springframework.boot.actuate.endpoint.jmx.EndpointMBean.invoke(EndpointMBean.java:126)
	at org.springframework.boot.actuate.endpoint.jmx.EndpointMBean.invoke(EndpointMBean.java:99)
	at java.management/com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.invoke(DefaultMBeanServerInterceptor.java:809)
	at java.management/com.sun.jmx.mbeanserver.JmxMBeanServer.invoke(JmxMBeanServer.java:801)
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl.doOperation(RMIConnectionImpl.java:1466)
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl$PrivilegedOperation.run(RMIConnectionImpl.java:1307)
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl.doPrivilegedOperation(RMIConnectionImpl.java:1399)
	at java.management.rmi/javax.management.remote.rmi.RMIConnectionImpl.invoke(RMIConnectionImpl.java:827)
	at java.base/jdk.internal.reflect.GeneratedMethodAccessor187.invoke(Unknown Source)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
	at java.rmi/sun.rmi.server.UnicastServerRef.dispatch(UnicastServerRef.java:359)
	at java.rmi/sun.rmi.transport.Transport$1.run(Transport.java:200)
	at java.rmi/sun.rmi.transport.Transport$1.run(Transport.java:197)
	at java.base/java.security.AccessController.doPrivileged(Native Method)
	at java.rmi/sun.rmi.transport.Transport.serviceCall(Transport.java:196)
	at java.rmi/sun.rmi.transport.tcp.TCPTransport.handleMessages(TCPTransport.java:562)
	at java.rmi/sun.rmi.transport.tcp.TCPTransport$ConnectionHandler.run0(TCPTransport.java:796)
	at java.rmi/sun.rmi.transport.tcp.TCPTransport$ConnectionHandler.lambda$run$0(TCPTransport.java:677)
	at java.base/java.security.AccessController.doPrivileged(Native Method)
	at java.rmi/sun.rmi.transport.tcp.TCPTransport$ConnectionHandler.run(TCPTransport.java:676)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
	at java.base/java.lang.Thread.run(Thread.java:834)
{% endhighlight %}

一开始以为是版本问题，把solr-7.3.1改为solr-8.0.0，结果还是一样，最后发现是SpringBoot的健康检测问题。

## 解决方案

关闭solr健康检测！！！

{% highlight yml %}
management:
  health:
    solr:
      enabled: false
{% endhighlight %} 

如果服务不需要检测的话，也可以考虑把所有检测全关了。

`management.health.*.enabled=false`

## SpringBoot健康检测依赖

{% highlight java %}
<dependency>
    <groupid>org.springframework.boot</groupid>
    <artifactid>spring-boot-starter-actuator</artifactid>
</dependency>
{% endhighlight %}

SpringBoot的健康检查项有很多，用于检测各服务是否可用，包括以下几种：

- ElasticsearchHealthIndicator
- JmsHealthIndicator
- MailHealthIndicator
- MongoHealthIndicator
- RabbitHealthIndicator
- RedisHealthIndicator
- SolrHealthIndicator