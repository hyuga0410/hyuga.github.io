---
layout:       post
title:        "前端静态资源追加版本号时间戳"
subtitle:     "maven打包，前端静态资源追加版本号时间戳，解决静态资源缓存问题"
date:         2019-07-10 20:42:05
author:       "Hyuga"
header-img:   "img/cover/head-top-16.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - maven
---

#### 起因
每次替换静态资源（css、js等），页面都要强刷或者清除缓存才能使更改的内容生效。

#### 目的
不想强刷或清除缓存而使更改内容生效。

#### 方案
使用maven插件com.google.code.maven-replacer-plugin插件 

在项目打包`package`时自动为静态文件追加类似`xxx.js?v=time`的后缀，从而解决上述问题。

效果如下：

{% highlight java %}
原始文件：
<script src="/static/css/about-businesss.css"></script>

打包后：
<script src="/static/css/about-businesss.css?v=0.0.1-SNAPSHOT-20190710011942"></script>
{% endhighlight %}

在`pom.xml`加入插件信息

{% highlight xml %}
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>

        <plugin>
            <groupId>com.google.code.maven-replacer-plugin</groupId>
            <artifactId>replacer</artifactId>
            <version>1.5.3</version>
            <executions>
                <!-- 打包前进行替换 -->
                <execution>
                    <phase>prepare-package</phase>
                    <goals>
                        <goal>replace</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!-- 自动识别到项目target文件夹 -->
                <basedir>${build.directory}</basedir>
                <!-- 替换的文件所在目录规则 -->
                <includes>
                    <include>**/templates/*.ftl</include>
                    <include>**/templates/**/*.ftl</include>
                    <include>**/static/*.css</include>
                    <include>**/static/**/*.css</include>
                    <include>**/static/*.js</include>
                    <include>**/static/**/*.js</include>
                    <include>**/static/*.eot</include>
                    <include>**/static/**/*.eot</include>
                    <include>**/static/*.svg</include>
                    <include>**/static/**/*.svg</include>
                    <include>**/static/*.ttf</include>
                    <include>**/static/**/*.ttf</include>
                    <include>**/static/*.woff</include>
                    <include>**/static/**/*.woff</include>
                    <include>**/static/*.woff2</include>
                    <include>**/static/**/*.woff2</include>
                </includes>
                <replacements>
                    <!-- 更改规则，在css/js文件末尾追加?v=时间戳，反斜杠表示字符转义 -->
                    <replacement>
                        <token>\.css\"</token>
                        <value>.css?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.css\'</token>
                        <value>.css?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.js\"</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.js\'</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.eot\"</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.eot\'</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.svg\"</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.svg\'</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.ttf\"</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.ttf\'</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.woff\"</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.woff\'</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                    <replacement>
                        <token>\.woff2\"</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\"</value>
                    </replacement>
                    <replacement>
                        <token>\.woff2\'</token>
                        <value>.js?v=${project.version}-${maven.build.timestamp}\'</value>
                    </replacement>
                </replacements>
            </configuration>
        </plugin>
    </plugins>
</build>
{% endhighlight %}

