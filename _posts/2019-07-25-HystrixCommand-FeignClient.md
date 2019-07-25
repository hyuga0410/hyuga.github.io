---
layout:       post
title:        "记一次熔断降级HystrixCommand和FeignClient之间传值的问题"
subtitle:     "一个头疼的线程代理问题"
date:         2019-07-25 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-16.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 疑难杂症
---

## 前言

有个`Freemarker-SpringBoot`项目，上下文拦截器会把每个请求中涉及到的用户信息、浏览器信息等放到一个`Context`对象中，再把`Context`对象放到`ContextHold`中，`ContextHold`对象如下：

{% highlight java %}
public class ContextHold {

    private static ThreadLocal<Context> contextThreadLocal = new ThreadLocal<>();


    public static Context get() {
        return contextThreadLocal.get();
    }


    public static void set(Context context) {
        contextThreadLocal.set(context);
    }

    public static void remove() {
        contextThreadLocal.remove();
    }

}
{% endhighlight %}

这样，在同一个请求线程中，service层可以任意使用`ContextHold`的对象。

如果项目一直单体玩倒也没所谓，不过最近开始拆分成微服务的模式，`web`层和`service`层拆分。

so！怎么通讯？把所有涉及到的参数都放到各个调用接口参数中？可行，改动太大。

想了很久，折中处理，保留`ContextHold`的模式，把`ContextHold`中的`Context`对象给传到`service`层。

#### 问题一
`web`和`service`之间是通过`feign`调用，生成了一个新的请求线程，`ThreadLocal`信息丢失，没法传到`service`. 

接着采用请求拦截方案！

`web`层添加`FeignRequestInterceptor`

{% highlight java %}
/**
 * Feign请求拦截器（设置请求头，传递请求参数）
 *
 * @author hyuga
 * date 2019-07-04 11:27
 * 说明：服务间进行feign调用时，不会传递请求头信息。
 * 通过实现RequestInterceptor接口，完成对所有的Feign请求,传递请求头和请求参数。
 */
@Component
public class FeignRequestInterceptor implements RequestInterceptor {
    @Override
    public void apply(RequestTemplate requestTemplate) {
        Context context = ContextHold.get();
        requestTemplate.header("context", JSON.toJSONString(context));
    }
}
{% endhighlight %}

`service`添加`ContextInterceptor`，用于从请求头中把`context`拿出来，反序列化

{% highlight java %}
/**
 * 上下文拦截器，用于往上下文中放入必要的信息
 *
 * @author hyuga
 * @date 2018-10-29
 */
public class ContextInterceptor extends ZBaseInterceptorAdapter {

    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        try {
            //微服务跨应用用户信息传递
            String context = request.getHeader("context");
            if (StringUtil.hasText(context)) {
                ContextHold.set(JsonFastUtil.json2Bean(context, Context.class));
            } else {
                ContextHold.set(new Context());
            }

            return super.preHandle(request, response, handler);
        } catch (Exception e) {
            logger.error(e.getMessage(), e);
            throw new RuntimeException(e.getMessage());
        }
    }
}
{% endhighlight %}

这样，`context`就顺利从`web`层传输到了`service`层，保住了原先的大部分代码逻辑，有些确实不好改动。

#### 问题二
**熔断降级引起的代理线程问题**

{% highlight java %}
@HystrixCommand(fallbackMethod = "pageAppointDown")
public Pagination<AppointPageVO> pageAppoint(AppointPageForm form) {
    return appointFeign.pageAppoint(form).pickBody();
}
{% endhighlight %}

因为采用了熔断降级，程序执行到`FeignRequestInterceptor`时，当前线程是新生成的代理线程，而不是原来的请求线程，所以`Context context = ContextHold.get();`拿出来的对象是`null`，导致`service`报错。

这个问题卡住了很久才定位到，最后的解决方案是采用：

{% highlight java %}
private static ThreadLocal<Context> contextThreadLocal = new InheritableThreadLocal<>(); 

代替 

private static ThreadLocal<Context> contextThreadLocal = new ThreadLocal<>(); 
{% endhighlight %}

原因的话，下面这片文章有很详细的讲解！

- 作者：[代码小司机](https://me.csdn.net/hewenbo111)
- [【InheritableThreadLocal——父线程传递本地变量到子线程的解决方式及分析】](https://blog.csdn.net/hewenbo111/article/details/80487252)

> 摘要：

上一个博客提到ThreadLocal变量的基本使用方式，可以看出ThreadLocal是相对于每一个线程自己使用的本地变量，但是在实际的开发中，有这样的一种需求：父线程生成的变量需要传递到子线程中进行使用，那么在使用ThreadLocal似乎就解决不了这个问题，难道这个业务就没办法使用这个本地变量了吗？答案肯定是否定的，ThreadLocal有一个子类InheritableThreadLocal就是为了解决这个问题而产生的，使用这个变量就可以轻松的在子线程中依旧使用父线程中的本地变量。

......
