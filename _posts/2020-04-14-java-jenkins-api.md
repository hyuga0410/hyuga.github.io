---
layout:       post
title:        "Java整合Jenkins"
subtitle:     "Java获取Jenkins-api相关信息"
date:         2020-03-31 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-12.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

## 前言
测试环境搭建了Jenkins用于打包部署，但是不是所有人都能看到【系统管理】-【全局设置】页面，导致经常会有部分同事不清楚哪个环境部署的是哪个分支，想用某个环境又担心动到其他已提测的分支。

所以考虑把Jenkins的configure页面中的环境变量抓取到另一个项目中（导航项目），便于所有人直观的查看各环境的变量配置。

![](/img/2020/2020-04/java-jenkins-1.png)

## 实现方案

> JSON-API（采用Jenkins的JSON-API接口获取信息）

`http://10.152.2.xxx:8089/jenkins/api/json`

**http://10.152.2.xxx:8089/jenkins/为项目名称**

Jenkins右下角有快捷入口【REST_API】，Jenkins提供了三种api（XML_API、JSON_API、Python_API）

`http://10.152.2.xxx:8089/jenkins/api/` 里面有各种用法。

里面能获取到视图primaryView、views和jobs等数据，但是没有configure的信息，不满足需求。

> 爬取页面信息

想通过爬取页面提取配置信息，但是怎么也没搞定登录那部分的处理，最终放弃。

> 通过JAVA_API

{% highlight java %}
<dependency>
    <groupId>com.offbytwo.jenkins</groupId>
    <artifactId>jenkins-client</artifactId>
    <version>0.3.8</version>
</dependency>
{% endhighlight %}

{% highlight java %}
JenkinsServer jenkinsServer = new JenkinsServer(new URI("http://10.152.2.xxx:8089/jenkins/configure"), "hyuga", "123456");
{% endhighlight %}

虽然获取到了jenkinsServer，但是。。。还是获取不到我想要的configure。

不过这种方式也可以通过Java代码获取很多Jenkins的任务信息，对于自定义Jenkins还是很有用的。

> Shell脚本 + JSOUP解析（最终方案）

加入JSOUP依赖包：
{% highlight java %}
<dependency>
    <!-- jsoup HTML parser library @ https://jsoup.org/ -->
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.11.3</version>
</dependency>
{% endhighlight %}

代码如下：
{% highlight java %}
public Map<String, Map<String, String>> getEnvList() {
    Map<String, String> javaEnv = new Hashtable<>();
    Map<String, String> vueEnv = new Hashtable<>();

    List<String> shellList = ListUtil.NEW();
    shellList.add("CRUMB=$(curl -s 'http://hyuga:11eb8a849a5b17a7f176ef46fd89da87fe@10.152.2.xxx:8089/jenkins/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,\":\",//crumb)')");
    shellList.add("curl -X POST -H \"$CRUMB\" http://hyuga:11eb8a849a5b17a7f176ef46fd89da87fe@10.152.2.xxx:8089/jenkins/configure");
    String shell = XshellUtil.shell(shellList);
    Elements elementsByClass = Jsoup.parse(shell).body().getElementsByClass("repeated-container").last().getElementsByClass("repeated-chunk");

    for (int i = 1; i < elementsByClass.size(); i++) {
        Elements elementsByClass1 = elementsByClass.get(i).getElementsByClass("setting-main");

        String envKey = elementsByClass1.get(0).select("input[value]").val();
        String envValue = elementsByClass1.get(1).select("input[value]").val();

        if (envKey.startsWith("release") || envKey.startsWith("test")) {
            javaEnv.put(envKey, envValue);
        } else {
            vueEnv.put(envKey, envValue);
        }
    }
    return MapUtil.NEW("java", javaEnv, "vue", vueEnv);
}
{% endhighlight %}

{% highlight java %}
public class XshellUtil {

    private final static Logger LOGGER = LoggerFactory.getLogger(XshellUtil.class);

    public static String shell(List<String> commands) {
        StringBuilder result = new StringBuilder();
        ListUtil.optimize(commands).forEach(command -> {
            // TODO Auto-generated method stub
            Process process;

            List<String> processList = new ArrayList<>();

            try {
                process = Runtime.getRuntime().exec(command);
                BufferedReader input = new BufferedReader(new InputStreamReader(process.getInputStream()));
                String line;
                while ((line = input.readLine()) != null) {
                    processList.add(line);
                }
                input.close();
            } catch (IOException e) {
                LOGGER.error("shell command execute error:{}", e.getMessage(), e);
            }
            for (String line : processList) {
                result.append(line);
            }
        });
        return result.toString();
    }

}
{% endhighlight %}

随手做的功能，并没有去优化，简单粗暴了点，先用着。

里面需要注意的一点是如何获取token。

- 访问`http://10.152.2.xxx:8089/jenkins/user/[登录用户名]/configure`
- 在API Token模块添加新的Token，保存后会生成token值，复制
- 黏贴到`CRUMB=$(curl -s 'http://hyuga:[token_value]@10.152.2.xxx:8089/jenkins/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,\":\",//crumb)')`
- 带上token value和$CRUMB，就可以直接调用configure页面的html信息
- 解析html，获取想要的数据







