---
layout:       post
title:        "ConditionalOnExpression简易使用"
subtitle:     "ConditionalOnExpression简易使用，RabbitMQ-topics模式用法及摸索"
date:         2019-12-28 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-19.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - SpringBoot
---

#### 前言

需要根据不同环境参数注入不同配置信息到SpringBoot项目中。

#### Demo

当读取配置文件`yhao.infra.common.env.deploy`值为`dev`或`rc`时，将注入下列配置，生成`TODO_NOTIFY`队列。

{% highlight java %}
@Configuration
@Import({RabbitMqConfiguration.class})
@ConditionalOnExpression("'${yhao.infra.common.env.deploy}'.equals('dev') || '${yhao.infra.common.env.deploy}'.equals('rc')")
public class RabbitMqOmsDevOrRcConfig {

    /**
     * 待办、通知队列
     */
    private static final String TODO_NOTIFY = TopicConstants.REDIS_PUBLISH_TODO_NOTIFY;

    @Bean(TODO_NOTIFY)
    public Queue todoNotifyA() {
        return new Queue(TODO_NOTIFY);
    }

}
{% endhighlight %}


当读取配置文件`yhao.infra.common.env.deploy`值为`pro`时，将创建队列`A`和`B`，且都绑定交换机`EXCHANGE_TOPIC_TODO_NOTIFY`。

{% highlight java %}
@Configuration
@Import({RabbitMqConfiguration.class})
@ConditionalOnProperty(name = "yhao.infra.common.env.deploy", havingValue = "pro")
public class RabbitMqOmsProConfig {

    /**
     * 待办、通知队列A
     */
    private static final String TODO_NOTIFY_A = TopicConstants.REDIS_PUBLISH_TODO_NOTIFY + ".A";

    /**
     * 待办、通知队列B
     */
    private static final String TODO_NOTIFY_B = TopicConstants.REDIS_PUBLISH_TODO_NOTIFY + ".B";

    /**
     * 交换机
     */
    private static final String EXCHANGE_TOPIC_TODO_NOTIFY = "EXCHANGE_TOPIC_TODO_NOTIFY";

    /**
     * 交换机与队列绑定的RoutingKey
     */
    private static final String Hyuga_TODO_NOTIFY_ROUTING_KEY = TopicConstants.REDIS_PUBLISH_TODO_NOTIFY + ".*";

    @Bean(TODO_NOTIFY_A)
    public Queue todoNotifyA() {
        return new Queue(TODO_NOTIFY_A);
    }

    @Bean(TODO_NOTIFY_B)
    public Queue todoNotifyB() {
        return new Queue(TODO_NOTIFY_B);
    }

    /**
     * 声明交换机
     */
    @Bean(EXCHANGE_TOPIC_TODO_NOTIFY)
    public Exchange exchangeTopicTodoNotify() {
        //声明了一个Topic类型的交换机，durable是持久化（重启rabbitmq这个交换机不会被自动删除）
        return ExchangeBuilder.topicExchange(EXCHANGE_TOPIC_TODO_NOTIFY).durable(true).build();
    }

    /**
     * 声明redis_publish_todo_notify队列和交换机绑定关系，并且指定RoutingKey
     *
     * @param queue    queue
     * @param exchange exchange
     * @return binding
     */
    @Bean
    public Binding bindHyugaNotifyTopicA(@Qualifier(TODO_NOTIFY_A) Queue queue, @Qualifier(EXCHANGE_TOPIC_TODO_NOTIFY) Exchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(Hyuga_TODO_NOTIFY_ROUTING_KEY).noargs();
    }

    @Bean
    public Binding bindHyugaNotifyTopicB(@Qualifier(TODO_NOTIFY_B) Queue queue, @Qualifier(EXCHANGE_TOPIC_TODO_NOTIFY) Exchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(Hyuga_TODO_NOTIFY_ROUTING_KEY).noargs();
    }

}
{% endhighlight %}

`dev`和`rc`单机环境，可以生成队列点对点。

`pro`集群环境，需要使用类`ActiveMQ`的`topic`订阅模式。

因为暂时没找到合适的方式，所以在代码中写死了`A`、`B`两个队列绑定交换机，通过通配符模式，加上`queueName.*`向交换机发送消息，这样不同环境的`A`和`B`队列就都能收到消息。

但是麻烦在于要在代码中写死不同队列名，已经在不同环境启动脚本中加入不同的参数`-Drabbitmq.topics.tags=A`，且代码监听配置需要添加`${rabbitmq.topics.tags}`。

> 具体代码如下 

{% highlight java %}
// 发Q代码 
rabbitTemplate.convertAndSend(TopicConstants.REDIS_PUBLISH_TODO_NOTIFY, getTopicsRoutingKey(TopicConstants.REDIS_PUBLISH_TODO_NOTIFY), JsonFastUtil.toJson(webSocketTopicVO));

/**
 * 如果是生产环境，则topic模式的routingKey需要符合模式规定
 * 注：RabbitMQ的topics模式较为麻烦，没理解清楚的话请不要随意更改topics相关的代码
 *
 * @param routingKey 路由键
 * @return 通配模式路由键
 */
public String getTopicsRoutingKey(String routingKey) {
    if (isProEnv()) {
        return routingKey + ".all";
    }
    return routingKey;
}

// 监听代码
@Slf4j
@Service("webSocketTodoAndNotifyTopic")
@RabbitListener(queuesToDeclare = @Queue("${redis_publish_todo_notify}${rabbitmq.topics.tags}"))
public class WebSocketTodoAndNotifyTopic {

}
{% endhighlight %}

#### 

#### Ps
这种肯定只能是过度方案，不然以后添加新的节点，还得往代码中追加一个`C`，实在太不雅观，后续还得花时间看看怎么改造。