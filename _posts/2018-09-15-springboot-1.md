---
layout:       post
title:        "SpringBoot2学习整理-01"
subtitle:     "整理SpringBoot2学习笔记"
date:         2018-09-15 22:42:05
author:       "Hyuga"
header-img:   "img/2018/2018-09/head-top-4.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - SpringBoot
---

## SpringBoot简介
- 与Spring同源，是简化Spring开发的一个框架.
- 整合了Spring全家桶（整合SSH/SSM/安全/docker/缓存/权限/消息/分布式/监控等等）.
- 内嵌tomcat容器，通过main方法启动，部署jar包运行项目，不需要配置启动tomcat.
- 整合了SpringMvc，只需要引入spring-boot-start-web依赖.
- 这几年微服务越来越火，SpringBoot面向SOA（http/webservice）的开发，配套SpringCloud --> 微服务（比原先的SOA更简单和高效）.
- 小项目和新项目首推SpringBoot开发，老项目如果不需要用SpringCloud则不建议转SpringBoot.
- SpringBoot几乎全部都是注解，没有配置.

## 微服务
> 微服务：一种架构风格（封装RPC远程调用）

原来的RPC通过HTTP/WebService形式调用，用起来复杂而笨重，类似直接用JDBC，后来发展到直接调用对象形式来调用微服务（Dubbo/Cloud），这种方式就叫做：微服务。

微服务其实就是封装了远程调用，比如：Dubbo，SpringCloud

## Hello World
SpringBoot官网：[https://projects.spring.io/spring-boot/](https://projects.spring.io/spring-boot/)

JDK8、Maven3.x、Intellij IDEA、SpringBoot2

创建项目，选择从Spring官网初始化和JDK

![](/img/2018/2018-09/boot-1.png)

定义group和article等信息

![](/img/2018/2018-09/boot-2.png)

选择需要的依赖

![](/img/2018/2018-09/boot-3.png)

输入项目名

![](/img/2018/2018-09/boot-4.png)

完成后，一个最简单的SpringBoot例子就已经可以运行了.结构如下：

![](/img/2018/2018-09/boot-5.png)

pom.xml文件的配置就不贴图了，按上面的配置完成后，配置就自己看吧，默认boot是最新版本.
{% highlight java %}
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        //SpringApplication.run(DemoApplication.class, args);
        System.out.println("hello world");
    }
}
{% endhighlight %}
搞定Hello World.

想使用WEB模式输入Hello World又该怎么办呢？

在pom.xml中添加SpringBoot的web依赖
{% highlight xml %}
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>LATEST</version>
</dependency>
{% endhighlight %}

这个依赖包已经内嵌了tomcat和一系列web所用到的功能，所以直接写一个controller，启动main方法即可调用web服务

{% highlight java %}
@Controller
public class DemoController {

    @RequestMapping("/")
    @ResponseBody
    String home() {
        return "Hello world";
    }

}
{% endhighlight %}

初始项目yml项目无任何配置，所以默认端口是8080，启动main后浏览器访问`localhost:8080`，即可看到Hello World.

不得不说，比起SpringMvc来说，这配置，这速度，不得不惊叹！！！

## SpringBoot打jar包
上面使用Spring官网初始化后的项目pom.xml中已经自动依赖了maven的打包依赖
{% highlight java %}
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
{% endhighlight %}
项目目录下构建命令 `mvn clean install`
或者直接用Idea工具进行打包构建，步骤简单就不贴图了，然后target下会生成一个项目jar包

## jar包启动服务
`java -jar /Users/hyuga/intellijProject/HyugaSpringBoot/target/demo-0.0.1-SNAPSHOT.jar`

终端跑下上面的命令，然后你会发现，服务启动了，启动日志打印了，浏览器能调用到web服务了...

tomcat呢？我都还没安装呢？啥配置都没弄，就执行了下jar包，结果web服务就启动好了？！！！

其实上面的例子才几个文件，但是打包后的jar包却有16M这么大，因为已经把项目用到依赖包和tomcat都已经打进去了，所以启动jar包的同时其实也是启动了内嵌的tomcat和tomcat下的web项目

以前搭个最简单的SpringMvc也是要花点时间的，调调配置啥的，SpringBoot是真的把这个时间都给你省下来了，只需要引入一个spring-boot-web依赖，如果只是要快速开发一个小项目，SpringBoot当真是不二之选。

## 静态资源访问
SpringBoot的静态资源放到src/main/resources/static文件夹，浏览器可以直接域名+文件全路径名访问，如：localhost:8080/图片.jpg

---
SpringBoot确实足够精简与高效，慢慢学习吧！


